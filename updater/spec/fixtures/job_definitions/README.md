### Job Definition Fixtures

These fixtures match the file format consumed by dependabot/cli.

#### Creating a fixture

We can generate these files from real projects using the CLI tool, e.g.

```sh
dependabot update bundler dependabot/dependabot-core -o test.yml
```

The resultant `test.yml` file will contain an `input` attribute which can be
extracted to use as a fixture, e.g.

```yml
input:
  # The entire input object can be extracted to a fixture file.
  job:
    package-manager: bundler
    ...
```

**Maintainers**: It is also possible to generate this file from the service,
refer to internal documentation.

## Funding

This project is funded through
[NGI0 Commons Fund](https://nlnet.nl/commonsfund), a fund established by
[NLnet](https://nlnet.nl) with financial support from the European Commission's
[Next Generation Internet](https://ngi.eu) program. Learn more at the
[NLnet project page](https://nlnet.nl/project/Land).

| Land                                                                                                                                                   | PlayForm                                                                                                                                                    | NLnet                                                                                         | NGI0 Commons Fund                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<img src="https://raw.githubusercontent.com/CodeEditorLand/Asset/refs/heads/Current/Logo/Land.svg" height="80px" alt="Land"  />](https://editor.land) | [<img src="https://raw.githubusercontent.com/PlayForm/Asset/refs/heads/Current/Logo/PlayForm.svg" height="80px" alt="PlayForm"  />](https://playform.cloud) | [<img width="240px" src="https://nlnet.nl/logo/banner.svg" alt="NLnet"  />](https://nlnet.nl) | [<img width="240px" src="https://nlnet.nl/image/logos/NGI0CommonsFund_tag_black_mono.svg" alt="NGI0 Commons Fund"  />](https://nlnet.nl/commonsfund) |
