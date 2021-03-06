# 0.77
- The `-g` option now keeps applications, as they are useful for
  computer platforms like the Atari ST and Commodore Amiga.
- The new `-y` option outputs a list of what titles have been
  kept and removed in the output dat.
- Refactored how Retool options get listed in the output name.
- Removed requirement for the !DOCTYPE element to exist that quotes
  the LogiqX DTD, so Retool can work with files from sites.dat. The
  dat is still validated against the DTD, however.
- Dealt with an edge case in selecting the right title if somehow
  there was both a version _and_ a revision of a title.
- Hid displaying options behind the `-?` option.

# 0.76
- Fixed filtering by language. This was broken due to a last minute
  change from a string to regex in the 0.75 release.
- Fixed folders not being processed to completion when filter by
  language was selected, and no valid titles were found in the
  current dat.
- Added `Ukranian` as a language.
- When filtering by language, if titles in the following regions don't
  have languages specified, they will be included if you select any of
  their respective languages:
  - Asia &mdash; English, Chinese, Japanese.
  - Hong Kong, Taiwan &mdash; Chinese, English.
  - Latin America &mdash; Spanish, Portuguese
  - South Africa &mdash; Afrikaans, English
  - Switzerland &mdash; German, French, Italian
  - Ukraine &mdash; Ukranian, Russian.
- Retool can now handle `rom` entries with no CRC specified. A `rom`
  entry must have at least a CRC, MD5, or SHA1, otherwise the title is
  dropped.
- The `-g` option now keeps preproduction titles, as they're treated as
  versions of titles instead of a separate thing, and many will be removed
  automatically on account of production versions existing. You will need
  to specify `-p` if you want to remove all preproduction titles.

# 0.75
- Now handles No-Intro dats. Note that grouping follows different rules to
  No-Intro. For example, in the Atari 2600 dat, a compilation is listed as
  a clone of a single title, despite featuring unique games. No-Intro also
  tends to include demos as clones of production titles. Retool also doesn't
  set clones for BIOSes, as you might need a different BIOS version in
  different situations. Retool also ignores titles that don't have `rom` or
  `disk` entries, which happens quite a bit in No-Intro's parent/clone sets.
- Added `Mexico` and `Hong Kong` as regions.
- Added `Zh-Hant` and `Zh-Hans` as languages.
- Alphas, betas, and prototypes are now treated as versions of titles.
  This way you get the highest version of a title available, and less noise
  without stripping everything away with the `-p` tag. Retool prefers production
  versions of titles, even if there is a preproduction title in a higher priority
  region. Priority is as follows:
  - Production with highest version
  - Production with highest revision
  - Production with no version/revision
  - Highest beta revision
  - Highest alpha revision
  - Highest prototype revision
- The exclude coverdiscs flag is now `-f`.
- You can now exclude bad dumps, pirate titles, promotional titles (titles that
  contain `(Promo)`, `EPK`, and `Press Kit`), and samples.
- Retool can now deal with dats that fail DTD validation due to `<clrmamepro>`
  and `<romcenter>` tags being in the wrong order in the header.
- The DTD file has been updated to take into account that even though people
  include it in their XML files, their XML files don't actually validate
  against it.
- Retool can now deal with dats that don't include MD5 or SHA-1 hashes.
- Windows and MacOS binaries have been removed. The Windows binary had
  performance issues due to the Python environment needing significant
  start up time, and I can no longer build the MacOS binary after the virtual
  machine stopped working.
- Some crash fixes and message format tweaking.

# 0.71
- Moved internal-config.json to the clonelists + metadata file so new
  releases of Retool aren't required each time the file is updated.

# 0.70
Oof, this is a big update, with feature and performance improvements all over
the place.

### Code readability and performance
Retool has been largely rewritten for readability, performance, security, and
to make forward momentum easier. This means a few breaking changes:

  - A new format for clone lists, so you need to update them. Retool is now
    better at automatically detecting different types of clones, so some titles
    no longer need to be listed in the clone lists.
  - The selected 1G1R titles might change compared to previous versions of
    Retool, due to a a major audit of clone lists, tags, and increases in
    parent detection accuracy.
  - Retool no longer exports parent/clone dats by default. Turns out the format
    can't really do the job properly, as it has no concept of priority within
    individual regions (and dealing with languages is a misery). As such, using
    dat managers like CLRMAMEPro or Romcenter to manage 1G1R can lead to
    unexpected outcomes. Instead, you now use Retool to produce the 1G1R dat
    you want, and only use the dat manager to manage your files.
  - Retool now has a minimum requirement of Python 3.8.

Processing dats is now also much faster as a result of better coding practices.
While this is nice for all dats, you'll mostly feel it on the big ones.
The PlayStation and IBM dats, for example, are now 2x faster.

### Custom region orders, filter by language
Custom region orders are now supported &mdash; even for supersets. You can
also filter by languages. Make your choices by editing the `region order` and
`filter languages` sections in `user-config.yaml`. You can use the `-l` option
to filter by language, or leave it off to include all languages.

### Other language and region stuff
Retool's a lot smarter with languages now.

For a start, implied languages are now enabled for most regions, and
language data for titles has been scraped from Redump's site. Redump doesn't
always include language data in the filename of their titles, so this assists
in more accurately selecting parent titles. For example, say your region
priority is USA, Europe. The USA title has CDs, but the European title has a
DVD version that we now know for sure supports English. Retool can now choose
the European DVD version over the USA CD version, depending on how clone lists
are set up.

The following things have also changed:

- A bunch of languages (`Af`, `Ca`, `Gd`, `Hr`, `Pa`, `Sk`, `Sl`, `Ta`) have
  been added.
- Slovenia has been added as a region.
- The incorrect `Gr` language code has been changed to `El`.
- Multi-region titles are now handled more elegantly.
- Fixed tags with regions in them causing a title to be filtered into the wrong
  region. For example, _Virtua Fighter 2 (Europe) (Rev A) (Made in USA)_ used
  to be categorized as a USA title -- it's now correctly identified as a
  European title.

### Other updates
- You can now exclude unlicensed titles.
- Sega ring codes are now mostly handled automatically for assigning clones.
  This has greatly reduced the size of some clone lists.
- Retool can now deal with "cloneof" tags in input dats, as Redump has started
  adding a few.
- Retool now handles Genteiban, Fukyuuban, and many other editions automatically.
- If the user specifies an output folder that doesn't exist, that folder is now
  created.
- Fixed problems that previously required overrides in clone lists.
- Added basic failure states for not finding required data in JSON config
  files.
- Dat header details are now escaped for valid XML, and XML file error handling
  is better.
- A bug in CLRMAMEPro dat conversions has been fixed.
- Dat file output is now human-ordered.
- The decision to include the version of a title on the newest operating
  system has been reversed. For the sake of compatibility, if there are
  multiple OS versions for a title, all are included.

# 0.60
- Removed pointless milliseconds from output file name.
- Added "Scholastic" to the publisher/distributor list.
- Added more disc synonyms.
- Automated handling PlayStation EDC titles.
- Language codes removed: `At`, `Be`, `Ch`, `Hr`.
- Languages codes added: `Bg`, `Cs`, `He`, `Ro`, `Tr`.
- Region added: Bulgaria, Romania. There are no games yet from these regions,
  but there are games with their languages.
- Extracted configuration data from the main Python script, and moved it into
  an external JSON file. This can potentially lead to greater user
  customization later.

# 0.59
- Added "Best of the Best" and "Best Hit Selection" as publishers/distributors.
- Added another disc synonym.

# 0.58
- Added yet another disc synonym.
- Added "Hitsquad - Regenerator" as a publisher/distributor.
- Removed _version.py.

# 0.57
- Separated removing coverdiscs from demos (-d), making it its own option (-b).
  Turns out plenty of full version games were given away as coverdiscs.
- Made handling the XML definition in a dat a bit more robust.
- Fixed importing of CLRMAMEPro dat files.
- When a parent or clone of a superset, override, or compilation title is not
  found, it no longer crashes Retool or silently fails, but tells you what's
  missing.
- Added "Teil" as a synonym for "Disc", to automatically pick up some German
  titles.
- Added a '-g' option, which is shorthand for all options (-abcdemps).
- Added Th as a language.
- Converted clone lists to JSON so they're more portable, and binaries don't
  require an update every time the clones update.
- Fixed a bug in selecting supersets.
- Noted that supersets only currently really work if you follow Retool region
  order. Otherwise current limitations in dat format and dat managers get in
  the way.

# 0.56
- Messed with the region order a little to be more fair to more popular
  languages, taking in mind how many titles were actually released for
  those languages.
- Bumped Portuguese up the priority list when deciding between two
  identical titles that support different languages.
- Moved stuff out of readme.md to make it more readable, and created
  a GitHub wiki for more detailed information.

# 0.55
- Code changes to support a build pipeline.

# 0.54
- Greatly sped up parent/clone processing, and removed XML conversion in
  favor of writing directly to the output dat. This will be mostly noticeable
  for large dats. The IBM and Sony Redump dats, for example, now process around
  10x faster, cutting a minutes long process for each into seconds on an
  i7 8700K.
- Added another distributor/publisher to check for when scanning for clones.
- Removed the option to remove titles with Alt tags, as it was a leftover
  from when Retool didn't do 1G1R properly.
- Added a counter for how many compilations were removed, if the option is
  enabled.

# 0.53
- Fixed a bug where if a region name was in a title, it caused clones to be
  assigned to the wrong parents. For example,
  _Daytona **USA** Deluxe Edition (Taiwan)_,
  _Cossacks II - Battle for **Europe** (Germany)_ would confuse the logic in
  Retool as to what region the title belonged to.
- Fixed a normalization bug when converting _Disc III_ into _Disc 3_ for title
  matching.

# 0.52
- Added better parent selection for the following:
  - Titles with multiple date versions.
  - Titles with distributor/publisher, OEM, and covermount tags.
  - Budget titles vs the originals.

# 0.51
- Added Zh as a language.
- Fixed a crash if a dat had no titles, and Retool was trying to process
  supersets.

# 0.50
- A huge architectural shift to move Retool to a 1G1R dat generator. Due to
  this, the `_regional_renames.py` file has been renamed to `_renames.py` and
    massively expanded to take into account localized title dupes. The file has
    also been reorganized to make future dupe additions easier.
- The `-re`, `-ra`, and `-en` options have been removed.
- The `-o` flag is no longer mandatory, and now defines an output folder.
  Output files are automatically named.
- You can now remove alternate (Alt) titles with the `-l` option.
- You can now promote supersets (for example, Game of the Year editions) to
  parents with the  `-s` option.
- Expanded demo removal criteria, as Redump does not always put demos in the
  "Demos" category.
- Region processing is now much faster.
- OEM and Hibaihin titles that have a matching commercial title with the same
  name are now marked as clones.
- Titles that aren't the latest revisions or versions are now marked as clones.
  This doesn't support release versioning, like 0.100 being larger than 0.99,
  however there's not much evidence of that style of versioning being used
  across Redump titles.
- Titles that include multiple regions are now deduped, preferencing titles
  with more regions. For example, out of **_Grim Fandango (USA)_** and
  **_Grim Fandango (USA, Europe)_**, the former is marked as a clone.
- Titles with the same name from the same region that include different
  language sets are now handled. The rules are quite complex:
  - If one title is in English, but the other isn't, mark the English title as
    the parent.
  - If one title from Europe has no languages listed, and the other has
    languages listed but English isn't one of them, mark the title with no
    languages listed as the parent (on the assumption that English may be in
    there).
  - If English is listed for both titles, and one title has more languages,
    mark the title with more languages as the parent.
  - If English is listed for both titles, and both titles have the same number
    of languages, check for preferred languages one by one, in the order listed
    below. The first title that doesn't support a preferred language is marked
    as a clone.
    1. Spanish
    1. French
    1. Japanese
    1. Portuguese
    1. German
    1. Italian
    1. Swedish
    1. Danish
    1. Norwegian
    1. Polish
    1. Greek
    1. Dutch
    1. Finnish
    1. Swiss
    1. Hungarian
    1. Russian
- Brazil and Latin America have been moved off the native English list. Modern
  games from these regions aren't guaranteed to have English translations.
- Japan has been moved up to second highest priority for non-native English
  regions, after Europe. The Asia region being higher priority than Japan was
  stealing titles that should have had Japanese parents.
- The requirement for the Logiqx doctype string in input files has been
  removed, as some non-Redump dats didn't have the string and were erroring.
- DTD validation has been added for Logiqx-style dat files. Redump dat files
  are invalid by default, as the category tag isn't in the spec. A modified DTD
  file has been included in the release with the category tag added, so Redump
  dats should pass.
- When you add a newer version of a dat to CLRMAMEPro that has been generated
  by Retool, it now triggers an update prompt where appropriate.
- Redump is no longer required to be the dat author.
- Characters that aren't valid in XML (<, >, &) have been escaped in generated
  dat files.
- Empty name, description, author, url, and version fields in dats are now
  handled, instead of crashing the program.
- Several bugs involving options flags were fixed.
- The title count was missing when dats were split into regions. This is now fixed.
- Bye bye ASCII logo. Vertical screen real estate is now more important.
- Added CloneRel tool, that exports an Excel file from a dat to better display
  existing parent/clone relationships.
- Dealt with the "_King's Field_ problem". _King's Field (Japan)_ didn't get a
  Western release. _King's Field_ in the USA is known as _King's Field II_ in
  Japan. **_King's Field II_** in the USA is **_King's Field III_** in Japan.
  Without extra logic, the program would mark all **_King's Field II_** titles as
  clones, which is not what we want.

# 0.34
- Added textwrap module for better readability on MacOS/Linux.
- Bundled MacOS and Windows binaries.

# 0.33
- Added ability to operate on folders.
- Fixed output file naming bugs.
- Fixed some user input issues.
- Tidied up some output for increased readability.
- Added the following regions: Argentina, Czech, Hungary, Singapore, Slovakia,
  Thailand, Turkey, Ukraine, United Arab Emirates.

# 0.32
- Reimplemented -re and -ra flags.
- Fixed header so the user wasn't prompted with false update warnings when the
  dat was loaded in CLRMAMEPro.

# 0.31
- Used dictionaries and classes to greatly increase performance and improve.
  code readability.
- Removed minidom dependency.
- Removed unused importlib dependency.
- Usability tweaks.

# 0.20
- Refactored for performance, code readability, and usability.
- Can handle CLRMAMEPro dat files now, not just Logiqx XML format.
- Added Germany, Ireland, Israel, Latin America, New Zealand, and Taiwan
  locales.
- Fixed user input bugs.
- Fixed excessive looping in some sections.
- Fixed title exclusion bugs.
- Added more error checking.
- Removed -re and -ra flags until refactor until next version.

# 0.10
- Initial release.