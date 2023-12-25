# RTPS protocol parser for Rust

This library implements message parsing for the RTPS protocol written in Rust.

DDS is a middleware protocol and API standard for data-centric connectivity. The main goal of DDS is to share the right data at the right place at the right time, even between time-decoupled publishers and consumers.

This library uses the code of [Dust DDS](https://github.com/s2e-systems/dust-dds), an awesome DDS library written in Rust. The motivation for this code extraction is to make the RTPS packet parsing capabilities available for other programs. For example a DDS middleware can inspect the RTPS messages, modify them on the fly or drop them based on some firewal logic.

## Example

Say you have a byte slice and you want to read its content:
```rust
let data = Arc::new([
    b'R', b'T', b'P', b'S', // Protocol
    2, 3, 9, 8, // ProtocolVersion | VendorId
    3, 3, 3, 3, // GuidPrefix
    3, 3, 3, 3, // GuidPrefix
    3, 3, 3, 3, // GuidPrefix
    0x15, 0b_0000_0011, 40, 0, // Submessage header
    0, 0, 16, 0, // extraFlags, octetsToInlineQos
    1, 2, 3, 4, // readerId: value[4]
    6, 7, 8, 9, // writerId: value[4]
    0, 0, 0, 0, // writerSN: high
    5, 0, 0, 0, // writerSN: low
    6, 0, 4, 0, // inlineQos: parameterId_1, length_1
    10, 11, 12, 13, // inlineQos: value_1[length_1]
    7, 0, 4, 0, // inlineQos: parameterId_2, length_2
    20, 21, 22, 23, // inlineQos: value_2[length_2]
    1, 0, 1, 0, // inlineQos: Sentinel
    0x07, 0b_0000_0101, 28, 0, // Submessage header
    1, 2, 3, 4, // readerId: value[4]
    6, 7, 8, 9, // writerId: value[4]
    0, 0, 0, 0, // firstSN: SequenceNumber: high
    5, 0, 0, 0, // firstSN: SequenceNumber: low
    0, 0, 0, 0, // lastSN: SequenceNumberSet: high
    7, 0, 0, 0, // lastSN: SequenceNumberSet: low
    2, 0, 0, 0, // count: Count: value (long)
]);
let msg = RtpsMessageRead::new(data);
println!("Received {} submessages", msg.submessages().len());
```

## License

This project keeps the license of the original work and is licensed under the Apache License Version 2.0.