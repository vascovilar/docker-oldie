Add Security Bundle
---------------------------
  1. `docker exec -it php composer require symfony/security-bundle`
  2. `docker exec -it php bin/console make:user` make class `User` via doctrine with field `username`
  3. `docker exec -it php bin/console make:migration`
  4. `docker exec -it php bin/console doctrine:migrations:migrate`
  5. `docker exec -it php bin/console security:encode-password` with pass `admin` and get encoded password
  6. Add `app/fixtures/user.yml` (replace encoded password and replace each $ by \\$)
```yaml
App\Entity\User:
    user_1:
        username: admin
        password: '\$argon2id\$v=19\$m=65536,t=4,p=1\$UthIkJd9SnCMIXxdvEPD+A\$oE34Nr6iXkse9T/FEGLdrgy+vsBUoC4xKtW4td1PTxs'
```
  7. `docker exec -it php bin/console hautelook:fixtures:load`

Add JWT authentification
---------------------------
  1. `docker exec -it php composer require lexik/jwt-authentication-bundle`
  2. `docker exec -it php mkdir -p config/jwt`
  3. `docker exec -it php openssl genrsa -out config/jwt/private.pem -aes256 4096` with pathphrase `de8dc53ffb0eac91d5881dfe1940fc8e`
  4. `docker exec -it php openssl rsa -pubout -in config/jwt/private.pem -out config/jwt/public.pem`
  5. `docker exec -it php chmod -R 644 config/jwt/*`
  6. Edit `app/config/packages/lexik_jwt_authentication.yaml` and replace
```yaml  
lexik_jwt_authentication:
    secret_key: '%env(resolve:JWT_SECRET_KEY)%'
    public_key: '%env(resolve:JWT_PUBLIC_KEY)%'
    pass_phrase: '%env(JWT_PASSPHRASE)%'
    token_ttl: '%env(JWT_TOKEN_TTL)%'
```
  7. Edit `app/.env` and replace
```yaml  
JWT_SECRET_KEY=%kernel.project_dir%/config/jwt/private.pem
JWT_PUBLIC_KEY=%kernel.project_dir%/config/jwt/public.pem
JWT_PASSPHRASE=de8dc53ffb0eac91d5881dfe1940fc8e
JWT_TOKEN_TTL=3600
```
  8. Edit `app/config/packages/security.yaml` and insert between `dev:` and `main:`
```yaml   
security: 
    firewalls:
        login:
            pattern:  ^/api/login
            stateless: true
            anonymous: true
            json_login:
                check_path: /api/login_check
                success_handler: lexik_jwt_authentication.handler.authentication_success
                failure_handler: lexik_jwt_authentication.handler.authentication_failure
        api:
            pattern:   ^/api
            stateless: true
            guard:
                authenticators:
                    - lexik_jwt_authentication.jwt_token_authenticator
``` 
```yaml   
security:     
    access_control:
        - { path: ^/api/login, roles: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/api,       roles: IS_AUTHENTICATED_FULLY }
``` 
  9. Edit `app/config/routes.yaml`
```yaml 
api_login_check:
    path: /api/login_check
    methods: ['POST']
``` 
  10. `docker exec -it php bin/console lexik:jwt:check-config`
  11. `curl -X POST -H "Content-Type: application/json" http://localhost/api/login_check -d '{"username":"admin","password":"admin"}'`
  12. You'll receive something like that
```json
{
   "token" : "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXUyJ9.eyJleHAiOjE0MzQ3Mjc1MzYsInVzZXJuYW1lIjoia29ybGVvbiIsImlhdCI6IjE0MzQ2NDExMzYifQ.nh0L_wuJy6ZKIQWh6OrW5hdLkviTs1_bau2GqYdDCB0Yqy_RplkFghsuqMpsFls8zKEErdX5TYCOR7muX0aQvQxGQ4mpBkvMDhJ4-pE4ct2obeMTr_s4X8nC00rBYPofrOONUOR4utbzvbd4d2xT_tj4TdR_0tsr91Y7VskCRFnoXAnNT-qQb7ci7HIBTbutb9zVStOFejrb4aLbr7Fl4byeIEYgp2Gd7gY"
}
```
