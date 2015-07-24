# clue/confgen [![Build Status](https://travis-ci.org/clue/php-confgen.svg?branch=master)](https://travis-ci.org/clue/php-confgen)

Configuration file generator (confgen) –
an easy way to take a *Twig template* and an arbitrary input data structure to
generate structured (configuration) files on the fly. 

> Note: This project is in beta stage! Feel free to report any issues you encounter.

## Input data

This project is all about transforming *your input data* structure.

As such, it makes no assumptions as to what kind of input data you're
dealing with, as long as it can be expressed in a simple JSON structure.
This project focuses on JSON input data for a few key reasons:

* Arbitrary data structure
  * Can contain pretty much any data structure
  * Simple, sane, strict data types
  * Flat or deeply nested structures
  * Schemaless by default – but offers options for using schema
* Ease of consuming (simple to read)
  * For both humans and machines alike
  * Easy to reason about
  * Maps well into dotted notation used in template files
* Ease of producing (simple to write)
  * Simple to convert into from many other common formats, such as YAML, XML, CSV, INI etc.
  * Very easy to write in PHP and many other languages
* Widespread use

Chances are, your input data *might* already be in a JSON file.
If it's not, then it's very easy to either convert with one of the many existing tools
or libraries or use some code similar to the following example:

```php
// $data = loadFromYaml('input.yml');
// $data = parseFromIni('input.ini');
$data = fetchFromDatabase();
file_put_contents('data.json', json_encode($data));
```

The structure of your input data file is entirely left up to you.
This library allows you to use any arbitrary input data structure.
For the following examples, this document assumes the following
(totally arbitrary) input data structure:

```json
{
    "timeout": 120,
    "interfaces": [
        {
            "name": "eth0",
            "address": "192.168.1.1"
        }
    ]
}
```

## Templates

Each (configuration) template file is broken into two parts:

* The optional, leading YAML front matter (or *meta-data* variables)
* And the actual Twig template contents

In its most simple form, a template without the optional YAML front matter would
look something like this:

```
timeout = {{ data.timeout }}
{% for interface in data.interfaces %}
auto {{ interface.name }}
    address {{ interface.address }}
{% endfor %}
```

If you also want to include *meta-data* variables, then
each section starts with a three-hyphen divider (`---`), so that a full file would
look something like this:

```
---
target: /etc/network/interfaces
chmod: 644
reload: /etc/init.d/networking reload
---
timeout = {{ data.timeout }}
{% for interface in data.interfaces %}
auto {{ interface.name }}
    address {{ interface.address }}
{% endfor %}
```

The individual sections are described in more detail in the following sections.

### Meta variables

The template files can optionally start with the meta-data in the form of a YAML front matter.
This syntax is quite simple and is pretty common for template processors and
static site generators such as [Jekyll](http://jekyllrb.com/docs/frontmatter/).

Documented variables:

* `target` target path to write the resulting file to
* `chmod` file mode of the resulting file
* `reload` command to execute after writing the resulting file
* `description` human readable description

You can also pass arbitrary custom meta-data.
See [meta-data schema](res/schema-template.json) for more details.

The meta variables will be accessible under the `meta` key in the Twig template.

### Template contents

Can contain any *Twig template*.

The input variables will be accessible under the `data` key.

The meta variables will be accessible under the `meta` key.
If no *meta-data* variables are present, then this key defaults to an empty array.

## Configuration

You can either parse/process individual template files or use a configuration
definition that allows you to process a number of files in one go.

In its most simple form, a JSON configuration structure looks like this:

```json
{
    "templates": "example/*.twig"
}
```

See [configuration schema](res/schema-confgen.json) for more details.

## Install

The recommended way to install this library is [through composer](http://getcomposer.org). [New to composer?](http://getcomposer.org/doc/00-intro.md)

```JSON
{
    "require": {
        "clue/confgen": "~0.3.0"
    }
}
```

## License

MIT
