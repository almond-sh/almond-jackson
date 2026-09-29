# almond-jackson

[Jackson](https://github.com/FasterXML/jackson)-based extensions of the API of the
[almond](https://github.com/almond-sh/almond) Scala Jupyter kernel: lets notebooks send
arbitrary values, serialized to JSON by Jackson, as display data or execute reply payloads.

Published as `json-api-jackson`, that used to live in the almond repository, up to almond 0.15.0.

## Usage

```scala
import $ivy.`sh.almond::json-api-jackson:_`
import almond.api.AlmondJackson.Extensions._
import almond.interpreter.api.DisplayData

case class Data(foo: String, ok: Boolean)
val data = Seq(Data("thing", true), Data("other", false))

publish.displayData("application/json", data)
publish.display(DisplayData().addJson("application/json", data))
publish.addPayloadObject(data)
```

The `_` version stands for the default version of the almond kernel. A specific version can be
passed instead.

## Building

```text
$ ./mill __.compile
$ ./mill __.mimaReportBinaryIssues
$ ./mill __.publishLocal
```

`json-api-jackson` is built once per binary Scala version (2.12, 2.13, 3), with the oldest Scala
version the almond kernels support for it, so that it can be used from all of them. It depends on
the oldest version of the almond API that has what it needs, so that it can be used along any later
almond version.

## Examples

The notebooks under `examples/notebooks/scala-<binary Scala version>` are run by
```text
$ ./mill examples.test
```
with released almond kernels (see `almondVersion` in `examples/package.mill`), which load the
json-api-jackson of this repository. Their outputs are compared with the ones they were committed
with. Run
```text
$ ALMOND_JACKSON_UPDATE_EXAMPLES=1 ./mill examples.test
```
to write the new outputs to the notebooks instead.

Jupyter runs from the environment described by `examples/pyproject.toml`, with exact versions
pinned in `examples/uv.lock`. It is managed with [uv](https://docs.astral.sh/uv/), that the build
downloads, so that nothing needs to be installed beforehand. To update the pinned Python
versions, run `uv lock --upgrade` from `examples`.

## Releasing

Pushing a `v*` tag uploads a release to Maven Central, to be published from the Central Portal.
Every push to `main` publishes a snapshot, whose version is derived from the latest `v*` tag (or
from 0.15.0, the last version published from the almond repository, if there's none).
