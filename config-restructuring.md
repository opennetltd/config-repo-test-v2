# Spring Cloud Config Restructuring

This document outlines the approach taken to restructure the Spring Cloud Config repositories.

## Restructuring Principles

1. **Base + Overrides Pattern** - Define common configurations in base files, with only genuine overrides in environment/country specific files
2. **Clear Directory Structure** - Organized by dimension (environment, country, service)
3. **DRY (Don't Repeat Yourself)** - Each configuration property defined exactly once
4. **Well-documented** - READMEs and comments explain the structure and reasons for overrides

## Example: Alive Service

### After Restructuring

#### Base Layers (config-repo-common)
- Service base: `services/alive.yml` - Base configuration applicable to all environments/countries
- Country base: `countries/application-ng.yml` - Nigeria-specific base settings
- Country+Service base: `countries/ng/alive.yml` - Nigeria-specific alive service settings

#### Environment Layers (config-repo-test-v2)
- Environment-specific: `alive-test.yml` - Only contains test-specific overrides
- Environment+Country: `countries/ng/alive-test.yml` - Only contains Nigeria-test-specific overrides

## Benefits

1. **Easier Maintenance** - Clear where to find and modify settings
2. **Reduced Duplication** - Each property defined once, overridden only when necessary
3. **Better Organization** - Logical grouping makes it easier to understand settings
4. **Easier Debugging** - Clear precedence path for configuration resolution
5. **Scalability** - Structure easily accommodates new services, countries, or environments

## Configuration Resolution Path

For a service (e.g., `alive`) in a specific environment (e.g., `test`) for a specific country (e.g., `ng`), Spring Cloud Config resolves configurations in this order:

1. `config-repo-common/application.yml` (global defaults)
2. `config-repo-common/services/alive.yml` (service defaults)
3. `config-repo-common/countries/application-ng.yml` (Nigeria defaults)
4. `config-repo-common/countries/ng/alive.yml` (Nigeria+alive defaults)
5. `config-repo-test-v2/application-test.yml` (test environment defaults)
6. `config-repo-test-v2/countries/ng/application-test.yml` (Nigeria+test defaults)
7. `config-repo-test-v2/alive-test.yml` (alive+test defaults)
8. `config-repo-test-v2/countries/ng/alive-test.yml` (Nigeria+alive+test specifics)

## Migration Strategy

Apply the same approach to other services, following these steps:

1. Identify the base configuration for each service
2. Create country-specific base overrides when needed
3. Clean environment-specific configs to contain only true overrides
4. Move country+environment configurations to the countries directory in environment repos
