# Gotta Query ’Em All, Again! Repeatable Name Resolution with Full Dependency Provenance

Work in progress

## Dataset

This dataset contains the raw queries captured by the resolver.
The input is the combination of the (outdated) Alexa List and the Majestic Million.
The embedded delegations for the `.com` domains have been extracted from the `.com` zone file.

The resolver queries `A`, `AAAA`, `CAA`, `MX` and `TXT` records for the input domains.
`www` subdomains (where reasonable) have been queried for `A` and `AAAA` records.
The resolver issued internal queries for address resolution (`A`, `AAAA`) and zone setup (`SOA`, `NS`, `DNSKEY`) as necessary.

The scan was executed on 2023-05-10.

### original resolution
[input](https://alcatraz.net.in.tum.de/naab-anrw2023/inputlist.zst)

#### resolver output
Used as input for speedbag run #1 and speedbag run #2.

[data](https://alcatraz.net.in.tum.de/naab-anrw2023/original/resolveout/)

### speedbag run #1


#### resolver output
Used as input for speedbag run #3.

[data](https://alcatraz.net.in.tum.de/naab-anrw2023/speedbag-run1/resolveout/)

#### speedbag output

[data](https://alcatraz.net.in.tum.de/naab-anrw2023/speedbag-run1/speedbagout/)

### speedbag run #2

#### speedbag output

[data](https://alcatraz.net.in.tum.de/naab-anrw2023/speedbag-run2/speedbagout/)

### speedbag run #3

#### speedbag output

[data](https://alcatraz.net.in.tum.de/naab-anrw2023/speedbag-run3/speedbagout/)

## Data Format

Data is compressed using [Zstandard](https://facebook.github.io/zstd/), commonly available as `zstd`.

### resolver output

#### nameserver.csv.zst
Important columns are:
`nsid` identifies the name server and is referenced in `queries.csv`.
`ip` provides the IP address.

#### queries.csv.zst
Important columns are:
`qid` query id, used for internal tracking.
`nsid` references the name server/IP address via `nameserver.csv`.
`srcport` and `proto` identify the local port and transport protocol.
`sent` provides a epoch time stamp in nano seconds, `inflight` gives the delta for the response.
`qname`, `qclass` and `qtype` identify the query.
`isedns` indicates EDNS0 (RFC 2671) usage.
`neterr`, `dnserr`,`generr` are various possible error.
Minimal DNS message content is provided in `flags`, `questioncount`, `answercount`, `authcount`, `addcount`.
`zid` internal references in which zone the query was executed.

Raw query data is stored in results.zip, the `roffset` provides the byte offset for the start of the response in this data structure.
`rlen` provides the length (which is also encoded in the data structure).
`qoffset` provides the byte offset for the query itself.

`prevq` and `isfinalq` track reties.
A query that has not been retried later is marked with `isfinalq`, `prevq` references a `qid` for which this query is a retry off.

#### results.zip

Is a zip container for the raw query data.
It contains multiple files starting at `querydata/0000000000000000.zst`.
Each one of the files contains about ~4MB raw query data, compressed with zstd.
The basename of the file `00000000003ffdec` contains the start offset of the contained data.

Example: The query `10187,475,44051,udp,1683751435217100208,34120135,f-dns.pl.,1,28,true,,,,33808,1,2,9,15,74,4193772,4193811,1095,0,true` points to `roffset` 4193811 (hex 0x3ffe13). This is contained in the file `querydata/00000000003ffdec.zst` at offset 39 (when considering uncompressed content, 4193811 - 0x3ffdec.

Each individual exchange is stored with a 2 byte length prefix (i.e. TCP DNS exchange framing).

### speedbag output

#### nameserver.csv.zst

#### unknownquery.csv.zst

#### unqueried.csv.zst
