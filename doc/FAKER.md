Add data faker
---------------------------
  1. `docker exec -it php composer require --dev hautelook/alice-bundle `
  2. Edit `app/config/packages/dev/nelmio_alice.yaml`
```yaml
nelmio_alice:
    locale: 'fr_FR'
```
  3. Add `app/fixtures/unicorn.yml`
```yaml
App\Entity\Unicorn:
    Unicorn_{1..10}:
        name: <name()>
        birthday: <dateTime()>
```
  4. `docker exec -it php bin/console hautelook:fixtures:load`
  5. go to https://localhost/unicorn

Example
---------------------------
  1. Edit `app/config/services.yaml`
```yaml
services:
    App\DataProvider\:
        resource: '../src/DataProvider'
        tags: ['nelmio_alice.faker.provider']
```
  2. Add `app/src/DataProvider/FixtureFakerProvider.php`
```php
<?php

namespace App\DataProvider;

class FixtureFakerProvider
{
    const UUID = [
        '3e502cb0320e2cc81c8ac25b25c1e2ba',
        '4a7ec81e0f5dec9e788bf1513eab8967',
        'b18c6373c7c682b0ca3d067f6b0d15df',
        'a3afdde1ffbf12ceaf0fb8abcd33682c',
        'a35c9b09e1b4ea9d6aade39ad494e27c',
        '577a4a88c5b7efe4fe8d1cdc574467af',
        'c6edbd984110e6343c1c9bf2883c8251',
        '16d71467922ef3f9c91cd4ae93c1163a',
        '6d5fcfce38fcb48c1aee395ad6221586',
        '381613eb148d371a0113303b21bb206b',
    ];

    const ADDR = [
        '26 rue de Picpus, 75012, Paris',
        '107 avenue du Général M.Bizot, 75012, Paris',
        '2 rue Dorian, 75012, Paris',
        '54 cours de Vincennes, 75012, Paris',
        '46 boulevard de Reuilly, 75012, Paris',
        '67 boulevard de Picpus, 75012, Paris',
        '25 rue du Sergent Bauchat, 75012, Paris',
        '44 avenue Daumesnil, 75012, Paris',
        '4 avenue du Bel Air, 75012, Paris',
        '58 avenue du Docteur Arnold Netter, 75012, Paris',
    ];

    private static $indexUuid;
    private static $indexAddress;
    private static $tmpAddress;

    public static function realSequentialUuid(): string
    {
        return self::UUID[self::$indexUuid++ % count(self::UUID)];
    }

    public static function realSequentialStreet(): string
    {
        self::$tmpAddress = explode(",", self::ADDR[self::$indexAddress++ % count(self::ADDR)]);

        return trim(self::$tmpAddress[0]);
    }

    public static function realSequentialPostCode(): string
    {
        return trim(self::$tmpAddress[1]);
    }

    public static function realSequentialCity(): string
    {
        return trim(self::$tmpAddress[2]);
    }

    public static function realPhone(): string
    {
        $n = '06';
        for ($i = 0; $i < 8; ++$i) {
            $n .= rand(0, 9);
        }

        return $n;
    }

    public static function realSiret(): string
    {
        $n = '';
        for ($i = 0; $i < 14; ++$i) {
            $n .= rand(0, 9);
        }

        return $n;
    }

    public static function realApeCode(): string
    {
        $n = '';
        for ($i = 0; $i < 3; ++$i) {
            $n .= rand(0, 9);
        }
        $c = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'Z'];
        $n .= $c[array_rand($c, 1)];

        return $n;
    }
}
```
  3. Add `app/fixtures/house.yml`
```yaml
App\Entity\House:
    house_1:
        uuid: <realSequentialUuid()>
        type: <randomElement(["Bar","Resto","Hôtel"])>
        company: <company()>
        street: <realSequentialStreet()>
        postalCode: <realSequentialPostcode()>
        city: <realSequentialCity()>
        phone: <realPhone()>
        siret: <realSiret()>
        apeCode: <realApeCode()>   
    house_{2..100}:
        uuid: <realSequentialUuid()>
        type: '90%? <randomElement(["Bar","Resto","Hôtel"])>'
        company: '90%? <company()>'
        street: '90%? <realSequentialStreet()>'
        postalCode: '90%? <realSequentialPostcode()>'
        city: '90%? <realSequentialCity()>'
        phone: '90%? <realPhone()>'
        siret: '90%? <realSiret()>'
        apeCode: '90%? <realApeCode()>'
```
