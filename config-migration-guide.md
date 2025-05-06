# Configuration Migration Guide

This guide outlines the process for migrating service configurations to the new structure across repositories.

## Migration Overview

The configuration repositories have been restructured to follow a clear layering approach with base configurations and specific overrides. This eliminates duplication and makes configurations more maintainable.

## Using Templates for Consistent Configuration

To reduce duplication, the restructuring uses templates for common configurations:

1. **Base Templates**:
   - `config-repo-common/countries/country-service-base.yml` - Template for country-specific settings
   - `config-repo-test-v2/countries/country-service-test-template.yml` - Template for test-specific country settings

2. **Inheritance Model**:
   - Country configs inherit and extend the base templates
   - Only true country-specific differences are explicitly defined
   - Templates use placeholders like `${country.short.code}` for dynamic values

## Handling Country Variants

In addition to standard country codes (ng, ke, gh, etc.), the configuration also supports country variants like:

- Integration environment variants: int, int1, int2, int3
- Nigeria variants: ng, ng0, ng1, ng2, ng3, ng4, ng5

These variants should be treated as separate "countries" in the configuration structure, with their own directories in both common and environment-specific repositories. They also inherit from the same templates as standard countries.

## Important Guidelines

1. **Common Repository Contains All Countries**
   - The config-repo-common repository must contain ALL country configurations
   - This includes configurations for countries that may only be used in specific environments
   - Country variants (int1, int2, int3, ng0-ng5) are treated as separate countries
   
2. **Country Directory Organization**
   - All country-specific files are now in the `countries/` directory:
     - Country application settings (`countries/application-{country}.yml`)
     - Country-specific service settings (`countries/{country}/{service}.yml`)
   - This provides a single location for all country-specific configurations

## Migration Steps for Each Service

1. **Identify Base Configuration**
   - Examine the service's configuration in `config-repo-common/services/{service}.yml`
   - Ensure it contains only settings common to all environments and countries

2. **Create Country-specific Base Configurations**
   - For each country that needs specific settings, create:
     - `config-repo-common/countries/{country}/{service}.yml`
   - Include only country-specific settings that apply to all environments

3. **Create Environment-specific Overrides**
   - In the environment repository (e.g., `config-repo-test-v2`):
     - Create/update `{service}-test.yml` with ONLY test-specific overrides
   - Remove any duplication from the base configuration

4. **Create Country+Environment Configurations**
   - For each country with environment-specific settings:
     - Create `config-repo-test-v2/countries/{country}/{service}-test.yml`
   - Include only settings specific to this country in this environment

5. **Remove Legacy Files**
   - Remove `{service}-test-{country}.yml` files from the root directory
   - These are replaced by the new structure in the countries directory

## Example: Alive Service

**Before restructuring:**
- Base: `config-repo-common/services/alive.yml` 
- Test: `config-repo-test-v2/alive-test.yml` (with duplication)
- Country+Test: `config-repo-test-v2/alive-test-ng.yml` 

**After restructuring:**
- Base: `config-repo-common/services/alive.yml` (enhanced with all common settings)
- Country Base: `config-repo-common/countries/ng/alive.yml` (only Nigeria-specific settings)
- Test: `config-repo-test-v2/alive-test.yml` (only true test-specific overrides)
- Country+Test: `config-repo-test-v2/countries/ng/alive-test.yml` (only Nigeria+test-specific overrides)

## Testing the Migration

After migrating each service:

1. **Verify Configurations**
   - Ensure all original settings are preserved across the new files
   - Check that there is no duplication between files

2. **Test in Development**
   - Deploy to development environment first
   - Verify that service behavior is unchanged

3. **Gradual Rollout**
   - Migrate one service at a time
   - Test thoroughly before proceeding to the next service

## Benefits

- Clearer organization
- Reduced duplication
- Easier maintenance
- Better visibility of environment and country differences
- Improved scalability for new countries or environments