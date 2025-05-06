# UAT Configuration Repository (V2)

This repository contains UAT environment-specific configurations for Spring Cloud Config, restructured to use the layered approach.

## Configuration File Types

1. **application-uat.yml** - Global UAT settings for all services and countries
2. **application-uat-{country}.yml** - Country-specific UAT settings
3. **{service}-uat.yml** - Service-specific UAT settings
4. **{service}-uat-{country}.yml** - Service and country-specific UAT settings

## Configuration Layering

Spring Cloud Config resolves properties in this order:
1. Global defaults from config-repo-common/application.yml
2. Service defaults from config-repo-common/services/{service}.yml
3. Country defaults from config-repo-common/application-{country}.yml
4. Environment defaults from config-repo-uat-v2/application-uat.yml
5. Environment + Country settings from config-repo-uat-v2/application-uat-{country}.yml
6. Service + Environment settings from config-repo-uat-v2/{service}-uat.yml
7. Service + Environment + Country settings from config-repo-uat-v2/{service}-uat-{country}.yml

## Environment Variables

Many settings use environment variables (e.g., ${MYSQL_HOST}) which are resolved at runtime. The environment variables must be set in the UAT environment.

## UAT Environment Specifics

- Increased logging levels for better debugging
- Testing APIs are enabled (Swagger, etc.)
- Payment providers are in test mode
- Lower resource allocations compared to production
- Debugging flags enabled where applicable