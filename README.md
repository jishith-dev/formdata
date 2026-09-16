# FormData

Multipart/form-data support for the Zen programming language.

The `formdata` package provides functionality for creating, encoding, parsing,
and saving multipart form data, including text fields and file uploads.

## Features

- Add text fields.
- Add files from the filesystem.
- Encode data as multipart/form-data.
- Parse multipart/form-data bodies.
- Retrieve text fields.
- Retrieve uploaded files.
- Save parsed files to disk.
- Support binary file data.
- Preserve filenames and content types.
- Built entirely in Zen.

## Installation

```bash
zen install formdata
```

## Import

```zen
import (FormData, MultipartFile, FormField) from "lib.zen"
```

## API

### FormData

#### `init()`

Initializes a FormData instance.

```zen
FormData form
form.init()
```

#### `append(name, value)`

Adds a text field.

**Returns:** `bool`

```zen
bool added = form.append("username", "Jishith")

screen(added)
```

#### `appendFile(name, filename, contentType)`

Reads a file from the filesystem and adds it to the form.

**Returns:** `bool`

```zen
bool added = form.appendFile(
    "document",
    "file1.txt",
    "text/plain"
)

screen(added)
```

#### `has(name)`

Checks whether a field exists.

**Returns:** `bool`

```zen
screen(form.has("username"))
```

#### `get(name)`

Retrieves a text field value.

**Returns:** `string`

```zen
string username = form.get("username")

screen(username)
```

#### `encode()`

Encodes the form as a multipart/form-data body.

**Returns:** `List<byte>`

```zen
List<byte> body = form.encode()
```

#### `contentType()`

Returns the Content-Type header containing the multipart boundary.

**Returns:** `string`

```zen
string header = form.contentType()

screen(header)
```

Example:

```text
multipart/form-data; boundary=----ZenFormBoundary7f3a91c2
```

#### `parse(body, header)`

Parses a multipart/form-data body.

**Parameters:**

- `body` — Raw multipart request body.
- `header` — Content-Type header containing the boundary.

**Returns:** `FormData`

```zen
FormData parsed = form.parse(body, header)
```

#### `getFile(name)`

Retrieves a parsed file by its form field name.

**Returns:** `MultipartFile`

```zen
MultipartFile uploaded = parsed.getFile("document")
```

---

### MultipartFile

Represents a parsed uploaded file.

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `name` | `string` | Form field name |
| `filename` | `string` | Original filename |
| `contentType` | `string` | MIME type |
| `data` | `List<byte>` | File contents |

#### `save(path)`

Saves file data to the specified path.

**Returns:** `bool`

```zen
bool saved = uploaded.save("uploads/output.txt")

screen(saved)
```

## Basic Example

```zen
import (FormData, MultipartFile, FormField) from "lib.zen"


FormData form
form.init()


form.append("username", "Jishith")
form.append("email", "jishith@example.com")


form.appendFile(
    "document",
    "file1.txt",
    "text/plain"
)


List<byte> body = form.encode()

string contentType = form.contentType()


fs.writeFileBytes("multipart.bin", body)


FormData parsed = form.parse(body, contentType)


screen(parsed.get("username"))
screen(parsed.get("email"))


MultipartFile file = parsed.getFile("document")

screen(file.name)
screen(file.filename)
screen(file.contentType)
screen(file.data.length)


bool saved = file.save("output.txt")

screen(saved)
```

## Multiple Files

Multiple files can be added to the same form.

```zen
form.appendFile(
    "first_file",
    "file1.txt",
    "text/plain"
)

form.appendFile(
    "second_file",
    "file2.txt",
    "text/plain"
)

form.appendFile(
    "binary_file",
    "binary.bin",
    "application/octet-stream"
)
```

Retrieve files after parsing:

```zen
MultipartFile first = parsed.getFile("first_file")
MultipartFile second = parsed.getFile("second_file")
MultipartFile binary = parsed.getFile("binary_file")
```

## Binary Files

The package supports binary file contents using `List<byte>`.

```zen
MultipartFile file = parsed.getFile("binary_file")

screen(file.data.length)

bool saved = file.save("output_binary.bin")

screen(saved)
```

Binary data is preserved during encoding, parsing, and saving.

## End-to-End Verification

The package was tested with:

- Multiple text fields.
- Multiple text files.
- A binary file containing raw byte values.
- Multipart encoding.
- Multipart parsing.
- Filename and content-type preservation.
- Saving parsed files.
- Byte-by-byte file integrity verification.

The tested encode → parse → save pipeline successfully preserved
the original file contents.

## License

MIT
