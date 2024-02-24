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

| Input                      | Default               | Required           | Description |
| -------------------------- | --------------------- | ------------------ | ----------- |
| `gpg-passphrase`           |                       | :heavy_check_mark: | The GPG passphrase to decrypt sw_core and krn_core installer |
| `sw-core-version`          | `5.3.3`               | :heavy_check_mark: | The SW Core version to install |
| `munit-version`            | `5.3.1+20240209-1641` | :heavy_check_mark: | The MUnit version to install |
| `krn-core-version`         | `0.4.0+20240221-0755` | :heavy_check_mark: | The KRN Core version to install |
| `krn-core-repository-file` | `''`                  | :negative_squared_cross_mark: | The file that contains references to the repositories we want to use to install other Mojos |

## :building_construction: Compile Smallworld product

To compile a Smallworld product, you can use the next example `.github.yml` and example `build.xml` (which is used by `ant`). This example refers to [another GitHub Action](https://github.com/krn-sebastiaan/actions/blob/main/README.md#compile):

### :rocket: `.github.yml`

```yaml
name: Compile mMojo

on:
  push:
    branches:
      - "**"
  pull_request:
    branches:
      - "**"

jobs:
  compile:
    runs-on: ubuntu-latest
    steps:
      - name: Compile mMojo
        uses: krn-sebastiaan/actions/compile@main
        with:
          gpg-passphrase: ${{ secrets.GPG_PASSPHRASE }}
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
