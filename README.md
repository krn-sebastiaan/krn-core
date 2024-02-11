# KRN Core

Action to easily install Smallworld Core, MUnit and KRN Core.
After installing these products, you can use KRN Core to create and test a Mojo (a packaged Smallworld product).

## :rocket: Usage

To set this up in your CI/CD, you need to do the following:

- Create a CI/CD secret in your project, called `GPG_PASSPHRASE`.
- Example `.github.yml` for the project:

```yaml
name: Install KRN Core

jobs:
  install:
    runs-on: ubuntu-latest
    steps:
      - uses: krn-sebastiaan/krn-core@main
        with:
          gpg-passphrase: ${{ secrets.GPG_PASSPHRASE }}
```

## :fountain_pen: Inputs

| Input              | Default               | Description                     |
| ------------------ | --------------------- | ------------------------------- |
| `gpg-passphrase`   |                       | The GPG passphrase to decrypt sw_core and krn_core installer |
| `sw-core-version`  | `5.3.3`               | The SW Core version to install |
| `munit-version`    | `5.3.1+20240209-1641` | The MUnit version to install |
| `krn-core-version` | `0.4.0+20240126-1532` | The KRN Core version to install |

## :building_construction: Compile Smallworld product

To compile a Smallworld product, you can use the next example `.github.yml` and example `build.xml` (which is used by `ant`):

### :rocket: `.github.yml`

```yaml
name: compile

on:
  push:
    branches:
      - "**"
  pull_request:
    branches:
      - "**"

env:
  KRN_CORE_VERSION: 0.4.0+20240126-1532
  PRODUCT_NAME: ${{ github.event.repository.name }}

jobs:
  compile:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-tags: 'true'
          fetch-depth: 0
      - name: Install SW Core, MUnit and KRN Core
        uses: krn-sebastiaan/krn-core@main
        with:
          gpg-passphrase: ${{ secrets.GPG_PASSPHRASE }}
          krn-core-version: ${{ env.KRN_CORE_VERSION }}
      - name: Create Mojo
        env:
          KRN_CORE_LOCATION: /opt/smallworld/krn_core-${{ env.KRN_CORE_VERSION }}
          SMALLWORLD_GIS: /opt/smallworld/core
          GITHUB_WORKSPACE: ${{ github.workspace }}
        run: ant make_mojo
      - name: Upload Mojo
        uses: actions/upload-artifact@v4
        with:
          name: ${{ env.PRODUCT_NAME }}
          path: ${{ env.PRODUCT_NAME}}*.jar
          if-no-files-found: error
```

### :ant: `build.xml`

```xml
<project>
  <property environment="env"/>

  <property name="mojo_strict" value="false"/>
  <property name="requires_jacoco_report" value="false"/>

  <import file="${env.KRN_CORE_LOCATION}/modules/mojo_lifecycle/resources/base/data/blueprint.xml"/>
</project>
```
