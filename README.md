Repository containing ASmap file attestations.

The [asmap-data](https://github.com/bitcoin-core/asmap-data) repository contains ASmaps for use with Bitcoin Core. This repository contains attestations on those files.

### ASmap Creation Process

The full ASmap generation process follows:
```
Generate ASmap -> encode ASmap -> attest to file hashes -> PR to this repo
(Kartograf)       (asmap-tool)    (asmap-attest) 
```

For usage instructions of these separate tools see [Kartograf](https://github.com/asmap/kartograf) and [asmap-tool](https://github.com/bitcoin/bitcoin/blob/master/contrib/asmap/README.md).
You must also ensure that the encoded files follow the naming conventions in [asmap-data](https://github.com/bitcoin-core/asmap-data), see also below.

### Files and Directory Structure

- `<run epoch>/<signer>`: each ASmap from a collaborative run is run at a specific epoch (Unix timestamp), which serves to identify a given run.
  - `SHA256SUMS`: hashes of the `final_result.txt`, filled and unfilled encoded ASmap
  - `SHA256SUMS.asc`: detached PGP signature over the `SHA256SUMS` file
- `builder-keys/<signer>.gpg`: signer keys

The attestation file should contain three lines, the first for the final result, the second for the filled ASmap and the third for the unfilled ASmap, for example:
```
cc199d5de04add6b5c2d95a72610c8a1a7b1f41fe01bd2b4c6db17795856aa31  final_result.txt
1146cbba8719cf3988d377df579667f68f97d2376d67755beb1e38194e196cfc  final_result_filled.dat
1c20ea2dee306af0a3ab4eaefaabe1e4c23a1c4256e60639e7ba48b2bbe56f24  final_result_unfilled.dat
```

### Script Usage

#### Attesting

To attest to an ASmap, you must have:
- the result file `final_result.txt` containing the ASmap in text format
- encoded the file via `asmap-tool.py` as both filled and unfilled versions
- the Unix timestamp associated with the ASmap run
- a PGP key added to the `builder-keys` dir in this repo

Attesting to an ASmap output:
```bash
env SIGNER=<gpg-key-name>\
  ASMAP_TXT=<path/to/final_result.txt>\
  ENCODED_FILLED=<path/to/filled.dat>\
  ENCODED_UNFILLED=<path/to/unfilled.dat>\
  EPOCH=<unix_timestamp>\
  ./asmap-attest
```

This will add a `SHA256SUMS` file and a `SHA256SUMS.asc` file under the `<EPOCH>/<SIGNER>` folder.

#### Verifying

Verifying attestations in this repo:
```bash
./asmap-verify
```
This will print out verifications of all attestations in the repo.
