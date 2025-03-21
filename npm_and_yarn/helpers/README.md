## Native JavaScript helpers

This directory contains helper functions for npm and yarn, natively written in
Javascript so that we can utilize the package managers internal APIs and other
native tooling for these ecosystems.

These helpers are called from the Ruby code via `run.js`, they are passed
arguments via stdin and return JSON data to stdout.

## Testing

When working on these helpers, it's convenient to write some high level tests in
JavaScript to make it easier to debug the code.

You can now run the tests from this directory by running:

```
yarn test path/to/test.js
```

### Debugging

In order to run an interactive debugger:

- `node --inspect-brk node_modules/.bin/jest --runInBand path/to/test/test.js`
- In Chrome, navigate to `chrome://inspect`
- Click `Open dedicated DevTools for Node`
- You'll now be able to interactively debug using the Chrome dev tools.

## Funding

This project is funded through
[NGI0 Commons Fund](https://nlnet.nl/commonsfund), a fund established by
[NLnet](https://nlnet.nl) with financial support from the European Commission's
[Next Generation Internet](https://ngi.eu) program. Learn more at the
[NLnet project page](https://nlnet.nl/project/Land).

| Land                                                                                                                                                   | PlayForm                                                                                                                                                    | NLnet                                                                                         | NGI0 Commons Fund                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<img src="https://raw.githubusercontent.com/CodeEditorLand/Asset/refs/heads/Current/Logo/Land.svg" height="80px" alt="Land"  />](https://editor.land) | [<img src="https://raw.githubusercontent.com/PlayForm/Asset/refs/heads/Current/Logo/PlayForm.svg" height="80px" alt="PlayForm"  />](https://playform.cloud) | [<img width="240px" src="https://nlnet.nl/logo/banner.svg" alt="NLnet"  />](https://nlnet.nl) | [<img width="240px" src="https://nlnet.nl/image/logos/NGI0CommonsFund_tag_black_mono.svg" alt="NGI0 Commons Fund"  />](https://nlnet.nl/commonsfund) |
