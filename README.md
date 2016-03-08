# The Vendor Tool for Go

Uses the go1.5+ vendor folder. Multiple workflows supported, single tool.

 * Copy existing dependencies from $GOPATH with `govendor add/update`.
 * If you ignore `vendor/*/`, restore dependencies with `govendor sync`.
 * Pull in new dependencies or update existing dependencies directly from
	remotes with `govendor fetch`.
 * Migrate from legacy systems with `govendor migrate`.
 * Supports Linux, OS X, Windows, probably all others.
 * Supports git, hg, bzr (must be installed an on the PATH).

 For an overview, see the [whitepaper](doc/whitepaper.md).

## Notes

 * Install with `go get -u github.com/kardianos/govendor`.
 * The project must be within a $GOPATH.
 * If using go1.5, ensure you `set GO15VENDOREXPERIMENT=1`.

### Quick Start
```
# Setup your project.
cd "my project in GOPATH"
govendor init

# Add existing GOPATH files to vendor.
govendor add +external

# View your work.
govendor list

# Look at what is using a package
govendor list -v fmt

# Specify a specific version or revision to fetch
govendor fetch golang.org/x/net/context@a4bbce9fcae005b22ae5443f6af064d80a6f5a55
govendor fetch golang.org/x/net/context@v1

# Update a package to latest, given any prior version constraint
govendor fetch golang.org/x/net/context
```

## Sub-commands
```
	init     Create the "vendor" folder and the "vendor.json" file.
	list     List and filter existing dependencies and packages.
	add      Add packages from $GOPATH.
	update   Update packages from $GOPATH.
	remove   Remove packages from the vendor folder.
	fetch    Add new or update existing packages from remote repository.
	sync     Pull in packages from remote respository to match vendor.json file.
	migrate  Move packages from a legacy tool to the vendor folder with metadata.
	
	(go tool wrapper for use with +status)
	fmt, build, install, clean, test, vet, generate
```

## Status

Packages can be specified by their "status".
```
	+local    (l) packages in your project
	+external (e) referenced packages in GOPATH but not in current project
	+vendor   (v) packages in the vendor folder
	+std      (s) packages in the standard library

	+unused   (u) packages in the vendor folder, but unused
	+missing  (m) referenced packages but not found

	+program  (p) package is a main package

	+outside  +external +missing
	+all      +all packages
```

Status can be referenced by their initial letters.

 * `+std` same as `+s`
 * `+external` same as `+ext` same as `+e`
	
Status can be logically composed:

 * `+local,program` (local AND program) local packages that are also programs
 * `+local +vendor` (local OR vendor) local packages or vendor packages
 * `+vendor,program +std` ((local AND program) OR std) vendor packages that are also programs
	or std library packages
 * `+vendor,^program` (vendor AND NOT program) vendor package that are not "main" packages.

## Package specifier

The full package-spec is: 
`<path>[::<origin>][{/...|/^}][@[<version-spec>]]`

Some examples:

 * `github.com/kardianos/govendor` specifies a single package and single folder.
 * `github.com/kardianos/govendor/...` specifies `govendor` and all referenced
	packages under that path.
 * `github.com/kardianos/govendor/^` specifies the `govendor` folder and all
	sub-folders. Useful for resources or if you don't want a partial repository.
 * `github.com/kardianos/govendor/^::github.com/myself/govendor` same as above
	but fetch from user "myself".
 * `github.com/kardianos/govendor/...@abc12032` all referenced packages at
	revision `abc12032`.
 * `github.com/kardianos/govendor/...@v1` same as above, but get the most recent
	"v1" tag, such as "v1.4.3".

## Packages and Status

You may specify multiple package-specs and multiple status in a single command.
Commands that accept status and package-spec:

 * list
 * add
 * update
 * remove
 * fetch

## Ignoring build tags
Ignoring build tags is opt-out and is designed to be the opposite of the build
file directives which are opt-in when specified. Typically a developer will
want to support cross platform builds, but selectively opt out of tags, tests,
and architectures as desired.

To ignore additional tags edit the "vendor.json" file and add tag to the vendor
"ignore" file field. The field uses spaces to separate tags to ignore.
For example the following will ignore both test and appengine files.
```
{
	"ignore": "test appengine",
}
```
