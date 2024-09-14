## TSDuck Test Suite

### TSDuck tests

The code of TSDuck is mainly divided in two parts, a large C++ library (`tsduck.dll` or `tsduck.so`)
and a collection of small command line tools and plugins.

Similarly, the tests for TSDuck are divided in two parts.

- The TSDuck library has its own unitary test suite based on a custom framework named "TSUnit".
  This test suite is part of the [main tsduck](https://github.com/tsduck/tsduck) repository for
  TSDuck in directory `src/utest`.
  This test suite is fully automated. The `utest` executable is built twice, once using the shared
  library and once using the static library. Both versions of the test suite are built and run using
  `make test`.
- The tools and plugins are less easy to test. They work on large transport stream files which
  would clutter the tsduck repository. The repository `tsduck-test` contains those tests and
  the relevant scripts and data files.

### Continuous integration

The TSDuck repository is integrated with "GitHub Actions", a continuous integration platform
which is directly hosted by GitHub. Each time a commit is pushed on GitHub and each time a
pull request is submitted, TSDuck is automatically rebuilt on Linux, Windows, macOS and all
tests are run on each platform. Thus, TSDuck is always validated on all operating systems,
even if a modification was previously compiled or tested on one of them only.

### Structure of test suite

In short, execute the script `run-all-tests.sh` to run the complete test suite.

The repository contains the following subdirectories:

- `tests` : Contains one script per test or set of tests. Each test script can be
  executed individually. All tests are executed using the script `run-all-tests.sh`.

- `common` : Contains utilities and common script.

- `input` : Contains input data files for the tests.

- `reference` : Contains reference output files for the various tests.

- `tmp` : Contains output files which are created by the execution of the tests.
  These files are typically compared against reference output files in `reference`.
  These files are temporary by definition. The subdirectory `tmp` is present on
  test machines only and is excluded from the Git repository.

- `extra` : Contains some additional tests which cannot be easily automated, either
  because they use specific hardware or because their results are not fully deterministic.
  These tests must be run manually.

### Adding new tests

To add a new test:

- Add input files in subdirectory `input`.

- Create the script `test-NNN.sh` in subdirectory `tests`. Use other existing
  test scripts as templates.

- Run the command `tests/test-NNN.sh --init`. If the test is properly written,
  this creates the reference output files in subdirectory `reference`. Manually
  check the created files, verify that they are correct. Be careful with this
  step since these files will be used as references.

- Run the same command without the `--init` option. This time, the output files
  are created in `tmp` and are compared with files in `reference`. Verify that
  all tests pass. Errors may appear if the test script is not properly written
  or if the output files contain unique, non-deterministic, time-dependent,
  system-dependent or file-system-dependent information. Make sure the output
  files are totally reproduceable in all environments. At worst, add code in
  the test script to remove any information from the output files which is
  known to be non-reproduceable.

Sometimes, TSDuck is modified in such in a way that an output file is modified
on purpose. Usually, this starts with a failed test. When analysing the test
failure, it appears that the modification of the output is intentional. In that
case, re-run the command `tests/test-NNN.sh --init` to update the reference
output files. Do not forget to manually validate them since they will act as
the new reference.

### Large files

This repository contains large files, typically transport stream files.

Initially, these files were not stored inside the regular GitHub repository.
Instead, they used the "[Git Large File Storage](https://git-lfs.github.com)" (LFS) feature of GitHub.
However, using LFS on GitHub happended to be a pain, as experienced by others and explained in
[this article](https://medium.com/@megastep/github-s-large-file-storage-is-no-panacea-for-open-source-quite-the-opposite-12c0e16a9a91).

As a consequence, the transport stream files were re-integrated into the Git
repository. But we now limit their size to 20 MB.

### Tests list

| Test | Description
| ---- | -----------
| 001  | Analyze an Astra live stream
| 002  | Analyze an Eutelsat Hot Bird live stream
| 003  | Analyze a live stream containing T2-MI encapsulation
| 004  | `tsdump` in raw mode
| 005  | `tstabcomp`
| 006  | `tspacketize`
| 007  | Various `tsp` plugins
| 008  | `zap` plugin
| 009  | `rmorphan` plugin
| 010  | Analyze a live stream with HEVC
| 011  | Adding CA descriptors in PAT and PMT
| 012  | Teletext, French language
| 013  | Teletext, Russian language
| 014  | SI analysis and conversions: EACEM private descriptors
| 015  | SI analysis and conversions: various descriptors
| 016  | Analyze an Eutelsat Hot Bird live stream with MPE data broadcast
| 017  | IP/MAC Notification Table (INT)
| 018  | Application Information Table (AIT)
| 019  | Integrity of help texts
| 020  | `pmt` plugin
| 021  | `tsp` stuffing options
| 022  | SI analysis and conversions: INT and AIT-specific descriptors
| 023  | `file` input plugin with more than one file
| 024  | `continuity` plugin
| 025  | Non-regression on a stream which crashed `tsanalyzer` (issue #49)
| 026  | Scrambling
| 027  | SCTE 35 Splice Information Table (SIT)
| 028  | SCTE 35 injection
| 029  | DVB-T bitrates
| 030  | `sections` plugin
| 031  | `inject` plugin with repetition rates
| 032  | DSM-CC stream events
| 033  | SI analysis and conversions: DSM-CC descriptors
| 034  | `tsswitch`
| 035  | `encap` and `decap` plugins
| 036  | `duplicate` plugin
| 037  | Tables with `preferred_section` XML attributes
| 038  | Tables with Czech diacritics
| 039  | `scrambler` plugin with multiple CW in text file
| 040  | Analyze an ATSC live stream
| 041  | `tsfixcc` and `continuity` plugin
| 042  | SI analysis and conversions: ATSC tables
| 043  | Using `--atsc` option on table analysis
| 044  | `craft` plugin
| 045  | `timeref` plugin
| 046  | `filter` plugin
| 047  | `remap` plugin
| 048  | `psimerge` plugin
| 049  | `continuity` plugin with duplicate packets
| 050  | SCTE 18 (Emergency Alert System) signalization
| 051  | Analyze and convert EIT's from an ATSC live stream
| 052  | Analyze and convert the RRT from an ATSC live stream
| 053  | SI analysis and conversions: NorDig private descriptors
| 054  | `timeshift` plugin
| 055  | SI analysis and conversions: BskyB private descriptors
| 056  | `nit` plugin with option `--build-service-list-descriptors`
| 057  | `hls` input plugin
| 058  | `--append` option in `file` output plugin
| 059  | `--ignore-absent` option in `zap` plugin
| 060  | UNT and its specific descriptors
| 061  | Order of sections in plugin `inject`
| 062  | Analyze a Japanese ISDB live stream
| 063  | SI analysis and conversions: ISDB signalization
| 064  | `pcrverify` plugin
| 065  | M2TS file format
| 066  | Preserve packet metadata in `fork` and `timeshift`
| 067  | SI analysis and conversions: DTG private descriptors
| 068  | EIT normalization
| 069  | Non-regression on `tsecmg` (issue #619)
| 070  | Non-regression on `fork` packet processor plugin (issue #633)
| 071  | RS204 file format
| 072  | Non-regression on `pes` plugin (issue #646)
| 073  | `stats` plugin
| 074  | `pes` plugin with access unit analysis
| 075  | Using non-standard time references
| 076  | `file` plugin with start/stop stuffing
| 077  | `tsxml`
| 078  | `--patch-xml` in various plugins
| 079  | `reduce` plugin
| 080  | Python bindings
| 081  | Java bindings
| 082  | Automated XML-to-JSON conversions
| 083  | `tsscan` with tuner emulator
| 084  | `splicemonitor` plugin
| 085  | `http` plugin
| 086  | `memory` plugins from Python
| 087  | `memory` plugins from Java
| 088  | `pcap` input plugin
| 089  | `hls` output plugin
| 090  | Option `--ignore-leap-seconds`
| 091  | Non-regression on SCTE 35 tables with unspecified command length (issue #764)
| 092  | Analyze a French DTTV live stream
| 093  | `pcrcopy` plugin
| 094  | `svresync` plugin
| 095  | `zap` plugin with multiple services
| 096  | `pidshift` plugin
| 097  | Non-regression on `tsanalyze`, `pes` and other plugins (assertion failure, issue #797)
| 098  | `eitinject` plugin
| 099  | `pcredit` plugin
| 100  | Non-regression on HEVC access unit formatting (issue #830)
| 101  | Non-regression on DuckContext not correctly passed from `tsp` to plugins `tables` and `psi`
| 102  | `tspcap` command
| 103  | `tspsi` with duplicated PMT after PAT change (issue #865)
| 104  | Non-regression test on EIT Schedule generation
| 105  | `tsemmg` and `datainject` plugin
| 106  | Pcap file with EMMG <=> MUX DVB SimulCrypt protocol
| 107  | Pcap file with ECMG <=> SCS DVB SimulCrypt protocol
| 108  | Non-regression test for a crash after using demuxed sections
| 109  | Non-regression test for packets without payload in `t2mi` plugin (issue #950)
| 110  | Plugin `splicemonitor` with option `--select-commands`
| 111  | Section filtering using binary pattern
| 112  | `tstables` with options `--no-duplicate` and `--invalid-version`
| 113  | `tscmp`
| 114  | `tsxml` with options `--merge` and `--sort`.
| 115  | SI analysis and conversions: VVC, EVC, LCEVC, AVS3 descriptors
| 116  | Preserve order of streams in a PMT
| 117  | Reorder streams in a PMT
| 118  | `tstabcomp` with `--eit-normalization`
| 119  | Non-regression on `scrambler` plugin (issue #1070)
| 120  | Non-regression on `tstables` with large truncated sections
| 121  | `tscrc32`
| 122  | SI analysis and conversions: SAT (DVB Satellite Access Table)
| 123  | Plugin `eitinject` with options `--synchronous-versions` and `--lazy-schedule-update`
| 124  | Plugin `sections` with option `--patch-xml`
| 125  | Non-regression on HEVC sequence parameter set analysis (issue #1191)
| 126  | Delete nodes and parents using XML patch file
| 127  | Non-regression on display of DVB service prominence descriptor (issue #1227)
| 128  | Plugin `feed`
| 129  | Plugin `ip`
| 130  | Plugin `srt`
| 131  | Plugin `rist`
| 132  | Non-regression on plugin `zap` and options `--audio-pid` and `--subtitles-pid`
| 133  | Option `--flush-last-unbounded-pes` in plugin `pes`
| 134  | Free TV Australia logical channel descriptor
| 135  | `pcap` input plugin with option `--http`
| 136  | `http` output plugin
| 137  | Non-regression on HEVC sequence parameter set analysis (issue #1369)
| 138  | Plugin `t2mi`
| 139  | Analyze a stream with HDMV registration and DTS-HD Master Audio
| 140  | Non-regression on incorrect command line argument declaration (issue #1389)
| 141  | Option `--meta-sections` in `tstables`
| 142  | Non-regression on tsp looping on corrupted TS packet (issue #1398)
| 143  | Non-regression on a crash in plugin `teletext` (issue #1400)
| 144  | Non-regression on a crash in plugin `pes` (issue #1401)
| 145  | Non-regression on corrupted HEVC sequence parameter set (issue #1405)
| 146  | Non-regression on corrupted HEVC stream (issue #1407)
| 147  | `tsfuzz` and plugin `fuzz`
| 148  | Non-regression on undetected VVC header (issue #1408)
| 149  | `rmsplice` plugin
| 150  | Teletext, text colors
| 151  | ATSC to DVB AC-3/E-AC-3 transformation in a PMT
| 152  | Non-regression on PCR computation in plugin `pcredit` (issue #1411)
| 153  | `aes` plugin
| 154  | Non-regression on event duplication in plugin `eitinject`
| 155  | `tables` plugin with option `--joint-termination`
| 156  | SI analysis and conversions: RCT
| 157  | Multicast, `ip`, `mpe` and `mpeinject` plugins
| 158  | Plugin `tables` with `--ip-udp`
| 159  | Non regression on incorrect use of stdout in `tstabcomp`
| 160  | Non-regression on incorrect SCTE 35 `program_splice_flag` (issue #1519)
| 161  | Plugin `nitscan` and `tsscan --nit-scan`
