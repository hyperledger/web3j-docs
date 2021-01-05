# web3j-docs
Web3j Documentation repository.

[docs.web3j.io](http://docs.web3j.io)

## Project setup

Make sure you have [Pipenv](https://docs.pipenv.org/en/latest/) installed.

Then run the following to get up and running:

```bash
git clone https://github.com/web3j/web3j-docs.git
cd web3j-docs
pipenv install
```

## Build instructions
Install mkdocs material theme:
```bash
pip install mkdocs-material
```
Install [mike](https://github.com/jimporter/mike)
```bash
pip install mike
```
Run locally using:

```bash
mike serve
```

To build and push to web3j-docs:

```bash
mike deploy VERSION ALIAS -p
```

Set default version to latest

```bash
mike set-default VERSION or ALIAS -p
```


