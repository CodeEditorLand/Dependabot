## `dependabot-docker`

Docker support for [`dependabot-core`][core-repo].

### Running locally

1. Start a development shell

```
$ bin/docker-dev-shell docker
```

2. Run tests
    ```
    [dependabot-core-dev] ~ $ cd docker && rspec
    ```

[core-repo]: https://github.com/dependabot/dependabot-core

### Supported tag schemas

Dependabot supports updates for Docker tags that use semver versioning, dates,
and build numbers. The Docker tag class is located at:
https://github.com/dependabot/dependabot-core/blob/main/docker/lib/dependabot/docker/tag.rb

#### Semver

Dependabot will attempt to parse a semver version from a tag and will only
update it to a tag with a matching prefix and suffix.

As an example, `base-12.5.1` and `base-12.5.1-golden` would be parsed as
`<prefix>-<version>` and `<prefix>-<version>-<suffix>` respectively.

That means for `base-12.5.1` only another `<prefix>-<version>` tag would be a
viable update, and for `base-12.5.1-golden`, only another
`<prefix>-<version>-<suffix>` tag would be viable. The exception to this is if
the suffix is a SHA, in which case it does not get compared and only the
`<prefix-<version>` parts are considered in finding a viable tag.

#### Dates

Dependabot will parse dates in the `yyyy-mm`, `yyyy-mm-dd` formats (or with `.`
instead of `-`) and update tags to the latest date.

As an example, `2024-01` will get updated to `2024-02` and `2024.01.29` will get
updated to `2024.03.15`.

#### Build numbers

Dependabot will recognize build numbers and will update to the highest build
number available.

As an example, `21-ea-32`, `22-ea-7`, and `22-ea-jdk-nanoserver-1809` are mapped
to `<version>-ea-<build_num>`, `<version>-ea-<build_num>`, and
`<version>-ea-jdk-nanoserver-<build_num>` respectively. That means only
"22-ea-7" will be considered as a viable update candidate for `21-ea-32`, since
it's the only one that respects that format.

## Funding

This project is funded through
[NGI0 Commons Fund](https://nlnet.nl/commonsfund), a fund established by
[NLnet](https://nlnet.nl) with financial support from the European Commission's
[Next Generation Internet](https://ngi.eu) program. Learn more at the
[NLnet project page](https://nlnet.nl/project/Land).

| Land                                                                                                                                                   | PlayForm                                                                                                                                                    | NLnet                                                                                         | NGI0 Commons Fund                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<img src="https://raw.githubusercontent.com/CodeEditorLand/Asset/refs/heads/Current/Logo/Land.svg" height="80px" alt="Land"  />](https://editor.land) | [<img src="https://raw.githubusercontent.com/PlayForm/Asset/refs/heads/Current/Logo/PlayForm.svg" height="80px" alt="PlayForm"  />](https://playform.cloud) | [<img width="240px" src="https://nlnet.nl/logo/banner.svg" alt="NLnet"  />](https://nlnet.nl) | [<img width="240px" src="https://nlnet.nl/image/logos/NGI0CommonsFund_tag_black_mono.svg" alt="NGI0 Commons Fund"  />](https://nlnet.nl/commonsfund) |
