# asn1

This is a minor fork of the `encoding/asn1` package. It adds in two tags for unmarshaling TLV data into structs:

- `littleendian` for little endian data
- `class` for explicit specification of expected class of tag (see [wiki article](https://en.wikipedia.org/wiki/X.690) for more details).

For example:

```go
type Info struct {
    FirmwareVersion    []byte `asn1:"tag:64,class:1"`
    SerialNumber       []byte `asn1:"tag:65,class:1"`
    PartNumber         []byte `asn1:"tag:66,class:1"`
    AccelSampleRate    int    `asn1:"tag:67,class:1,littleendian"`
    MagsenseSampleRate int    `asn1:"tag:68,class:1,littleendian"`
    AccelFFTSize       int    `asn1:"tag:69,class:1,littleendian"`
    MagsenseFFTSize    int    `asn1:"tag:70,class:1,littleendian"`
}

func main() {
    // 3 indicates Private specification
    // 0 indicates Primitive (octets directly encode data)
    // 5c is the number of bytes to follow 
    // the rest is described in the struct tags
    bytes, _ := hex.DecodeString("305c" + "5f402373696c766572676c6164652f6663635f76302e302e322d37342d6736636639636134005f410e554e494e495449414c495a4544005f420e554e494e495449414c495a4544005f430200195f440220035f450200085f46028000")

    var info Info

    _, err := Unmarshal(bytes, &info)

    if err != nil {
        panic(err)
    }

    fmt.Printf("%+v\n", info)
    fmt.Println(string(info.FirmwareVersion))
    fmt.Println(string(info.SerialNumber))
    fmt.Println(string(info.PartNumber))
}
```
