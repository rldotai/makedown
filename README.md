# makedown

A comfy way convert Markdown to (PDF|HTML|whatever) using `pandoc` and `make`.

If you write in Markdown, `pandoc` is extremely helpful for converting the source to a more generally useful form.
However, the default workflow is a bit of a hassle: you probably want to use a modified template, there are a lot of command line switches to remember, and sometimes a lot of intermediate files cluttering up the directory.
There are other scripts and tools that attempt to fix this, but I wanted something more minimal that still leaves ample room for customization.

# Setup

1. Copy `makedown/Makefile` to the directory you'll be working in.
```bash
wget https://raw.githubusercontent.com/rldotai/makedown/master/makedown/Makefile
```
2. Modify the Makefile as desired.
3. Run `make initialize` to create directories for source, build, and output.
    - The `source` directory is where you should store your Markdown documents as `.md` files.
    - The `output` directory will contain the generated PDFs.
    - `build` is used for the build process, to ensure separation between source/output files and any intermediate files that get generated.
4. Run `make pdfs`.

## Example

See the `./example` directory, where you will be treated to a first-person account of a README generating a PDF of itself and other paranormal happenings.

## Customization

Everything's specified in the Makefile, so if you want to change what directories to process or add/remove options for `pandoc`, then that's where you should make your changes.

If you want to use a custom template, you need to modify the `Makefile` so that it knows where it's located.
For convenience, I've added rules for downloading the templates provided by the author of `pandoc`, as well as my own slightly customized versions of the same.
They can be acquired via `make download-pandoc-templates` and `make download-cool-templates`, respectively.

## Dependencies

- You *should* already have `pandoc` and `make` available, otherwise this is probably all very confusing.
- You should also have a TeX engine (like `pdflatex` or `xelatex`) available and located where `pandoc` can find it.
- I'm assuming you have other standard shell utilities available as well (`sed` and `wget` for example), but these are currently only used for displaying help messages and downloading supporting files if needed.
- (Optional): For rebuilding on file changes, you can [install `entr`](https://github.com/eradman/entr) and then run `make watch`.
    - It's a useful utility regardless, [with documentatation available here](http://eradman.com/entrproject/).


# Details

Asides from enforcing a reasonably tidy directory structure, all this really does is wrap some commands that I frequently forget, with options that I find myself constantly looking up, using templates I constantly copy from folder to folder.

I like my files to look a certain way, but typing out:

```bash
pandoc  +RTS -K512m -RTS \
 --to pdf \
--from markdown+yaml_metadata_block+autolink_bare_uris-auto_identifiers+tex_math_single_backslash+raw_attribute+header_attributes \
--highlight-style tango \
--pdf-engine xelatex --strip-comments \
--metadata date="`date "+%B %e, %Y"`" \
-V graphics=yes \
-V fontsize=12pt \
--output README.pdf \
README.md
```

every time is a bit wearying.

# TODO

- [ ] Generate HTML files in addition to PDFs.
- [ ] Make the "cool" templates cooler.
    - Particularly the preamble.
- [ ] Automate creating new source files with YAML header
- [ ] Is there a better way to do help messages?
