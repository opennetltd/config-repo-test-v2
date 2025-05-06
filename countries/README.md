# Country-specific Configuration Directory

This directory contains country-specific overrides for the test environment. The structure follows the pattern:

```
countries/
├── country-service-test-template.yml  # Template for country-specific test settings
├── {country-code}/                    # Country directory (e.g., ng for Nigeria)
│   ├── application-test.yml           # Country-specific application settings
│   ├── {service}-test.yml             # Country-specific service settings
│   └── ...
└── ...
```

## Principles

1. **Only include overrides** - These files should only contain settings that differ from:
   - The common repository's base settings
   - The common repository's country-specific settings
   - The test environment's global settings
   - The country-service-test-template.yml template

2. **Country-specific settings** - These files define configuration values specific to a country within the test environment

3. **Clear documentation** - Always include comments explaining why settings are overridden for this specific country+environment combination

4. **Use templates** - The `country-service-test-template.yml` provides common test environment settings for all countries

## Template Usage

The `country-service-test-template.yml` defines standard settings that apply to most countries in the test environment:

- It specifies common timeout, batch size, and debug settings for the test environment
- Individual country test files should only be created if they have actual overrides from the template
- If a country needs only the standard settings, no country-specific file is needed
- Country-specific verification markers can be set in the country-specific files as needed
- This significantly reduces duplication and ensures consistent configurations across countries

## Configuration Resolution Order

When a service (e.g., `alive`) is deployed in a specific environment (e.g., `test`) for a specific country (e.g., `ng`), Spring Cloud Config resolves the configuration in this order:

1. `config-repo-common/application.yml` (global defaults)
2. `config-repo-common/services/alive.yml` (service defaults)
3. `config-repo-common/countries/application-ng.yml` (Nigeria defaults)
4. `config-repo-common/countries/ng/alive.yml` (Nigeria+alive defaults)
5. `config-repo-test-v2/application-test.yml` (test environment defaults)
6. `config-repo-test-v2/countries/ng/application-test.yml` (Nigeria+test defaults)
7. `config-repo-test-v2/alive-test.yml` (alive+test defaults)
8. `config-repo-test-v2/countries/ng/alive-test.yml` (Nigeria+alive+test specifics)