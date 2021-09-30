Add functional test
---------------------------
  1. `docker exec -it php composer require --dev symfony/phpunit-bridge`
  2. `docker exec -it php bin/console make:functional-test` with controller `Unicorn`
  3. Edit `app/tests/UnicornTest.php`
```php
        $client = static::createClient();
        $crawler = $client->request('GET', '/unicorn/');
        $this->assertSame(200, $client->getResponse()->getStatusCode());
        $this->assertContains('Unicorn index', $crawler->filter('h1')->text());
```
  4. `docker exec -it php bin/phpunit`

Example
---------------------------
```php
/* app/src/tests/ApiTestCase */

namespace App\Tests;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;
use Symfony\Component\HttpFoundation\Response;
use Hautelook\AliceBundle\PhpUnit\RefreshDatabaseTrait;

class ApiTestCase extends WebTestCase
{
    /*
     * Reload fixtures then revert at the end
     */
    use RefreshDatabaseTrait;

    protected $web;
    protected static $token;

    /**
     * For each test run this.
     */
    protected function setUp()
    {
        parent::setUp();
        $client = static::createClient();

        /* get token once */
        if (empty(static::$token)) {    
            $client->request(
                'POST',
                'api/login_check',
                [],
                [],
                ['CONTENT_TYPE' => 'application/json'],
                json_encode([
                  'username' => 'admin',
                  'password' => 'admin',
                ])
            );
            $data = json_decode($client->getResponse()->getContent(), true);
            static::$token = $data['token'];
        }

        /* create client */
        $this->web = $client;
        $this->web->setServerParameter('HTTP_Authorization', sprintf('Bearer %s', static::$token));
    }

    /**
     * Make an HTTP call to router.
     */
    protected function request(string $method, string $uri, $content = null, array $headers = []): Response
    {
        $server = ['CONTENT_TYPE' => 'application/json', 'HTTP_ACCEPT' => 'application/json'];
        foreach ($headers as $key => $value) {
            if ('content-type' === strtolower($key)) {
                $server['CONTENT_TYPE'] = $value;
                continue;
            }
            $server['HTTP_'.strtoupper(str_replace('-', '_', $key))] = $value;
        }
        if (is_array($content) && false !== preg_match('#^application/(?:.+\+)?json$#', $server['CONTENT_TYPE'])) {
            $content = json_encode($content);
        }
        $this->web->request($method, $uri, [], [], $server, $content);

        return $this->web->getResponse();
    }

    /**
     * Find entity in database.
     */
    protected function findOneEntity(string $className, array $search)
    {
        $repo = static::$container->get('doctrine')->getRepository($className);

        return $repo->findOneBy($search);
    }
}
```
