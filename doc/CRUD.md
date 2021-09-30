Add CRUD page
---------------------------
  1. `docker exec -it php bin/console make:entity` (table `Unicorn`, name string, birthday datetime)
  1. `docker exec -it php bin/console make:crud`
  1. `docker exec -it php bin/console make:migration`
  1. `docker exec -it php bin/console doctrine:migrations:migrate`
  1. `docker exec -it php bin/console debug:route`
  1. go to https://localhost/unicorn

Add simple material design theme
---------------------------
  1. Edit `app/templates/base.html.twig` and add these lines to end of `<head>` tag:
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/css/materialize.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/js/materialize.min.js"></script>
<style type="text/css">body{width:700px;margin:auto;} form,table{margin:20px;} a{padding-right:20px;} .btn{display:block; float:right;}</style>
```
  2. Modify `app/src/Form/UnicornType.php`
```php
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\DateTimeType;

        $builder
            ->add('name', TextType::class)
            ->add('birthday', DateTimeType::class, [
                // renders it as a single text box
                'widget' => 'single_text',
            ])
        ;
```
  3. go to https://localhost/unicorn