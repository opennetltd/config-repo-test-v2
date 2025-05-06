# UAT Configuration Repository (V2)

This repository contains UAT environment-specific configurations for Spring Cloud Config, restructured to use the layered approach (Base + Overrides/Overlays)

## Directory Structure

- `application-test.yml` - Global UAT settings for all services and countries
- `{service}-test.yml` - Service-specific UAT settings (e.g., `alive-test.yml`)
- `shared-*-test.yml` - Shared configuration modules for specific subsystems (database, Redis, etc.)
- `countries/` - Country-specific configurations
  - `country-service-test-template.yml` - Template for country-specific service configurations
  - `countries/{country}/application-test.yml` - Country-specific UAT settings
  - `countries/{country}/{service}-test.yml` - Country+Service-specific UAT settings (only for actual overrides)

## Shared Configuration Files

To reduce duplication, we've extracted common subsystem configurations into shared files:

- `shared-datasource-test.yml` - Database connection settings for the test environment
- `shared-redis-test.yml` - Redis configuration for the test environment
- `shared-server-test.yml` - Server and Tomcat settings for the test environment
- `shared-management-test.yml` - Monitoring and API endpoints for the test environment
- `shared-messaging-test.yml` - Messaging system settings for the test environment

These shared files are imported by `application-test.yml` and contain only the test-specific overrides of the base configurations in config-repo-common.

## Configuration Layering

Spring Cloud Config resolves properties in this order:
1. Global defaults from config-repo-common/application.yml
2. Service defaults from config-repo-common/services/{service}.yml
3. Country defaults from config-repo-common/countries/application-{country}.yml
4. Country+Service defaults from config-repo-common/countries/{country}/{service}.yml
5. **Shared configurations** from config-repo-test-v2/shared-*-test.yml (imported by application-test.yml)
6. Environment defaults from config-repo-test-v2/application-test.yml
7. Environment+Country settings from config-repo-test-v2/countries/{country}/application-test.yml
8. Service+Environment settings from config-repo-test-v2/{service}-test.yml
9. Country+Service+Environment settings from config-repo-test-v2/countries/{country}/{service}-test.yml

### Verification of Property Resolution

To verify the correct property resolution, Spring Cloud Config provides several indicators:

1. **Configuration verification markers**: Each country+service combination includes verification markers 
   (e.g., `config-server-check: properties_from_config_server_alvght` for Ghana's alive service in test).
   These markers confirm that the correct files are being loaded.

2. **Property resolution tracing**: You can enable property resolution tracing in Spring Boot by setting:
   ```
   logging.level.org.springframework.cloud.config=TRACE
   ```

3. **Endpoint verification**: Spring Cloud Config provides an actuator endpoint to inspect active properties:
   ```
   https://{service-host}/actuator/configprops
   ```

4. **Resolution example**: For the alive service in Ghana's test environment, properties would resolve in this order:
   - Base defaults (configuration properties for all services)
   - alive service defaults (RocketMQ settings, thread pool defaults)
   - Ghana defaults (country code, language, timezone) 
   - Ghana + alive defaults (country-specific topic settings)
   - Shared test settings (database, Redis configurations)
   - Test environment defaults (test-specific thread pool settings)
   - Ghana + test settings (Ghana-specific test environment settings)
   - alive + test settings (reduced thread counts, debug logging)
   - Ghana + alive + test settings (Ghana-specific connection timeout overrides)

## Configuration Principles

1. **Only include overrides** - Files should only contain settings that differ from base configurations
2. **DRY (Don't Repeat Yourself)** - Define each configuration property exactly once
3. **Clear layering** - Follow the hierarchy to make configurations maintainable
4. **Well-documented** - Add comments explaining why settings are overridden
5. **Use shared modules** - Extract common subsystem configurations into shared files

## Country Variants

This repository supports both standard country codes and country variants:

- **Standard countries**: ng (Nigeria), ke (Kenya), gh (Ghana), etc.
- **INT environments**: int, int1, int2, int3
- **Nigeria variants**: ng0, ng1, ng2, ng3, ng4, ng5

Each country variant is treated as a separate "country" with its own directory structure:

```
countries/
├── ng/                       # Nigeria 
│   ├── application-test.yml  # Nigeria test settings
│   ├── alive-test.yml        # Nigeria-specific alive test settings
│   └── ...
├── ng1/                      # Nigeria-1 variant
│   ├── application-test.yml  # Nigeria-1 test settings
│   ├── alive-test.yml        # Nigeria-1-specific alive test settings
│   └── ...
└── ...
```

## Environment Variables

Many settings use environment variables (e.g., ${MYSQL_HOST}) which are resolved at runtime. The environment variables must be set in the UAT environment.

## UAT Environment Specifics

- Increased logging levels for better debugging
- Testing APIs are enabled (Swagger, etc.)
- Payment providers are in test mode
- Lower resource allocations compared to production
- Debugging flags enabled where applicable
