# spring-cloud-vault-demo

This is a simple project to demonstrate differences in configuration of `spring-cloud-starter-vault-config` `Hoxton` vs `2020.0.x` and `spring-boot` `2.3.x` vs `2.4+` 

There are 2 issues demonstrated:
1. inability to provide Vault configuration override in profile specific file in spring-boot 2.4+ and spring-cloud-starter-vault-config 3+
2. inability to disable Vault in profile specific file and multi-document yaml file in spring-boot 2.4+ and spring-cloud-starter-vault-config 3+

Run Vault locally and store a secret in Vault
```
vault kv put secret/demo secret=success 
```
Export env variables
```
export VAULT_HOST=
export VAULT_PORT=
export VAULT_SCHEME=
export VAULT_TOKEN=

export KEYSTORE=
export KEYSTORE_PASSWORD=
export TRUSTSTORE=
export TRUSTSTORE_PASSWORD=
```
Activate dev profile
```
export SPRING_PROFILES_ACTIVE=dev
```

- Vault configuration can be overridden in profile specific file in `spring-boot` 2.3.x `spring-cloud-starter-vault-config` Hoxton
```
./gradlew :demo-2-3-profile-specific-file:bootRun
```

- Vault configuration can be disabled in profile specific file in `spring-boot` 2.3.x `spring-cloud-starter-vault-config` Hoxton
```
./gradlew :demo-2-3-vault-disabled-in-profile-specific-file:bootRun
```

- Vault configuration **cannot** be overridden in profile specific file in `spring-boot` 2.4+ `spring-cloud-starter-vault-config` 2020.0.x

```
./gradlew :demo-2-4-profile-specific-file:bootRun
```

By default, authentication is set to AWS_IAM. TOKEN authentication should be used in dev profile. This is not taken into account and exception is thrown
```
org.springframework.vault.authentication.VaultLoginException: Cannot login using AWS-IAM: missing client token; nested exception is org.springframework.web.client.HttpClientErrorException$BadRequest: 400 Bad Request: "{"errors":["missing client token"]}<LF>"
```

- Vault configuration *can* (surprisingly) be overridden in multi-document yaml profile file in `spring-boot` 2.4+ `spring-cloud-starter-vault-config` 2020.0.x
```
./gradlew :demo-2-4-multidocument-yaml-profile:bootRun
```

- Vault configuration **cannot** be disabled in profile specific file in `spring-boot` 2.4+ `spring-cloud-starter-vault-config` 2020.0.x
```
./gradlew :demo-2-4-vault-disabled-in-profile-specific-file:bootRun
```

By default, authentication is set to AWS_IAM. Vault configuration should be disabled in dev profile. This is not taken into account and exception is thrown
```
org.springframework.vault.authentication.VaultLoginException: Cannot login using AWS-IAM: missing client token; nested exception is org.springframework.web.client.HttpClientErrorException$BadRequest: 400 Bad Request: "{"errors":["missing client token"]}<LF>"
```

- Vault configuration **cannot** be disabled in multi-document yaml profile file in `spring-boot` 2.4+ `spring-cloud-starter-vault-config` 2020.0.x

```
./gradlew :demo-2-4-vault-disabled-multidocument-yaml-profile:bootRun
```

By default, authentication is set to AWS_IAM. Vault configuration should be disabled in dev profile. This is not taken into account and the app fails with
```
Config data location 'vault:' does not exist
```

## Rationale 

Our applications' configuration is environment variables driven. We do not use spring profiles when we deploy an application. Spring profiles are used only in local development where we most often do not need to run Vault locally or we run Vault in dev mode with root token available.


