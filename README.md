# formdata

**Author:** Jishith M P
**Version:** 2.0.0

Multipart/form-data for Zen. Build a form with text fields and files, encode it into a request body, or parse an incoming body back into fields and files. Binary data is kept byte for byte.

## Install

```bash
zen install formdata
```

## Import

```zen
import (FormData, MultipartFile) from "formdata"
```

## Quick start

```zen
import (FormData, MultipartFile) from "lib.zen"

FormData form
form.append("username", "jishith")
form.append("email", "jishith@example.com")
form.appendFile("avatar", "photo.png", "image/png")

List<byte> body = form.encode()
string header = form.contentType()

FormData reader
FormData parsed = reader.parse(body, header)

screen(parsed.get("username"))

MultipartFile avatar = parsed.getFile("avatar")
avatar.save("avatar_copy.png")
```

A new `FormData` is ready to use as soon as it is declared. There is no `init()` call.

## FormData

| Member | Type | Description |
|--------|------|-------------|
| `fields` | `List<MultipartFile>` | Text fields. Each entry uses `name` and `value`. |
| `files` | `List<MultipartFile>` | Added or parsed files. |

### `append(name, value)`

Adds a text field. Returns `bool`.

```zen
form.append("username", "jishith")
```

### `appendFile(name, path, contentType)`

Reads the file at `path` and adds it to the form. `contentType` is optional and defaults to `application/octet-stream`. Returns `false` if the file does not exist.

The `path` you pass is stored as the filename.

```zen
form.appendFile("doc", "hello.txt", "text/plain")
```

### `has(name)`

Returns `true` if a text field or a file with that name exists.

### `get(name)`

Returns the value of a text field, or an empty string if there is none.

### `getFile(name)`

Returns the `MultipartFile` with that name. If there is none, you get an empty one with an empty `name` and no data.

### `remove(name)`

Removes the first text field or file with that name. Returns `false` if nothing matched.

### `encode()`

Returns the multipart body as `List<byte>`.

### `contentType()`

Returns the header value to send with the body.

```text
multipart/form-data; boundary=----ZenFormBoundary7f3a91c2
```

### `parse(body, header)`

Parses a multipart body and returns a new `FormData`. The boundary is read from `header`. If the header has no boundary, or the body is empty or malformed, the result is empty.

`parse` is an instance method, so call it on a declared `FormData`:

```zen
FormData reader
FormData parsed = reader.parse(body, header)
```

## MultipartFile

| Property | Type | Description |
|----------|------|-------------|
| `name` | `string` | Form field name |
| `filename` | `string` | Filename |
| `contentType` | `string` | MIME type |
| `data` | `List<byte>` | File contents |
| `value` | `string` | Text value, used by text fields |

### `save(path)`

Writes `data` to `path`. Returns `bool`.

```zen
MultipartFile doc = parsed.getFile("doc")
doc.save("out_hello.txt")
```

## Example output

Encoding a small form:

```zen
FormData small
small.append("city", "Kochi")
small.append("age", "21")
List<byte> raw = small.encode()
```

gives this body:

```text
------ZenFormBoundary7f3a91c2
Content-Disposition: form-data; name="city"

Kochi
------ZenFormBoundary7f3a91c2
Content-Disposition: form-data; name="age"

21
------ZenFormBoundary7f3a91c2--
```

Parsing a body that has three files:

```text
parsed doc: hello.txt (text/plain, 21 bytes)
parsed notes: notes.txt (text/plain, 29 bytes)
parsed photo: photo.bin (application/octet-stream, 300 bytes)
```

## Multiple files

```zen
form.appendFile("first", "file1.txt", "text/plain")
form.appendFile("second", "file2.txt", "text/plain")
form.appendFile("binary", "binary.bin", "application/octet-stream")

MultipartFile first = parsed.getFile("first")
MultipartFile second = parsed.getFile("second")
MultipartFile binary = parsed.getFile("binary")
```

## Notes

- The boundary and the parsing helpers are private.
- Only `FormData` and `MultipartFile` are exported.
- Names are matched exactly. If two entries share a name, the first one wins for `get`, `getFile` and `remove`.

## Changes in 2.0.0

- Removed `init()`. Fields have default values.
- Removed `FormField`. Text fields are stored as `MultipartFile`.
- Added `remove(name)`.
- Internal helpers and the boundary are now private.
- `parse` is documented as an instance method.

## License

MIT
