Add API support
---------------------------
  1. `docker exec -it php composer require nelmio/api-doc-bundle` answer `Yes` to 'Do you want to execute this recipe?'
  2. `rm -rf app/config/routes/nelmio_api_doc.yaml` (remove routes we'll never use)
  3. Edit `app/config/routes.yaml`
```yaml  
api_doc:
    path: /api
    methods: GET
    defaults: { _controller: nelmio_api_doc.controller.swagger_ui }
```
  4. Edit `app/config/packages/security.yaml`
```yaml  
security:
    firewalls:
        api_doc:
            pattern: ^/api$
            security: false
    access_control:
        - { path: ^/api$, roles: IS_AUTHENTICATED_ANONYMOUSLY }
```
  5. Edit `app/config/packages/nelmio_api_doc.yaml`
```yaml  
nelmio_api_doc:
    documentation:
        components:
            securitySchemes:
                Bearer:
                    type: http
                    scheme: bearer
                    bearerFormat: JWT        
```
  6. `docker exec -it php bin/console make:controller` named `ApiHouse`
  7. `rm -rf app/templates/api_house` (remove twig templates we'll never use)
  8. Edit `app/src/Controller/ApiHouseController.php`
```php
use App\Repository\UnicornRepository;
use App\Entity\Unicorn;
use Symfony\Component\HttpFoundation\JsonResponse;
use Nelmio\ApiDocBundle\Annotation\Model;
use Nelmio\ApiDocBundle\Annotation\Security;
use OpenApi\Annotations as OA;

    /**
     * @Route("/api/house", name="api_house", methods={"GET"})
     * @OA\Response(
     *     response=200,
     *     description="Returns the unicorn in the house",
     *     @OA\JsonContent(
     *        type="array",
     *        @OA\Items(ref=@Model(type=Unicorn::class, groups={"read"}))
     *     )
     * )
     * @OA\Tag(name="house")
     * @Security(name="Bearer")
     * @param UnicornRepository   $unicornRepository
     * @return JsonResponse
     */
    public function index(UnicornRepository $unicornRepository): JsonResponse
    {
        $unicorns = $unicornRepository->findAll();

        return $this->json($unicorns, 200, [], ['groups' => 'read']);
    }
```
  9. Add group in `app/src/Entity/Unicorn.php`
```php
use Symfony\Component\Serializer\Annotation\Groups;

      /**
       * @ORM\Column(type="string", length=255)
       * @Groups("read")
       */
      private $name;
```
  10. get a token `curl -X POST -H "Content-Type: application/json" http://localhost/api/login_check -d '{"username":"admin","password":"admin"}'`
  11. go to https://localhost/api (paste hash into authorize box without keyword 'Bearer')