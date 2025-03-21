# File parsers

File parsers take a set of dependency files and extract a list of dependencies
for the project.

There is a `Dependabot::FileParsers` class for each language Dependabot
supports.

## Public API

Each `Dependabot::FileParsers` class implements the following methods:

| Method   | Description                                                                                                                                                                             |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `#parse` | Returns an array of `Dependabot::Dependency` instances, representing the dependencies for the project. Each `Dependabot::Dependency` has a `name`, `version` and a `requirements` array |

An integration might look as follows:

```ruby
require 'dependabot/file_parsers'

files = fetcher.files

parser_class = Dependabot::FileParsers::Ruby::Bundler
source = Dependabot::Source.new(provider: 'github', repo: "gocardless/business")
parser = parser_class.new(dependency_files: files, source: source)

dependencies = parser.parse

puts "Found the following dependencies: #{dependencies.map(&:name)}"
```

## Writing a file parser for a new language

All new file parsers should inherit from `Dependabot::FileParsers::Base` and
implement the following methods:

| Method                  | Description                                                                    |
| ----------------------- | ------------------------------------------------------------------------------ |
| `#parse`                | See Public API section.                                                        |
| `#check_required_files` | Raise a runtime error unless an appropriate set of files is provided. Private. |

To ensure the above are implemented, you should include
`it_behaves_like "a dependency file parser"` in your specs for the new file
parser.

## Funding

This project is funded through
[NGI0 Commons Fund](https://nlnet.nl/commonsfund), a fund established by
[NLnet](https://nlnet.nl) with financial support from the European Commission's
[Next Generation Internet](https://ngi.eu) program. Learn more at the
[NLnet project page](https://nlnet.nl/project/Land).

| Land                                                                                                                                                   | PlayForm                                                                                                                                                    | NLnet                                                                                         | NGI0 Commons Fund                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<img src="https://raw.githubusercontent.com/CodeEditorLand/Asset/refs/heads/Current/Logo/Land.svg" height="80px" alt="Land"  />](https://editor.land) | [<img src="https://raw.githubusercontent.com/PlayForm/Asset/refs/heads/Current/Logo/PlayForm.svg" height="80px" alt="PlayForm"  />](https://playform.cloud) | [<img width="240px" src="https://nlnet.nl/logo/banner.svg" alt="NLnet"  />](https://nlnet.nl) | [<img width="240px" src="https://nlnet.nl/image/logos/NGI0CommonsFund_tag_black_mono.svg" alt="NGI0 Commons Fund"  />](https://nlnet.nl/commonsfund) |
