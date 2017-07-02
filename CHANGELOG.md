# Change Log
All notable changes to this project will be documented in this file. This
project adheres to [Semantic Versioning](http://semver.org/).

## [2.6.4]

### Fixed

 * A regression that prevented environment names to be specified as a symbol.
 * A regression that prevented the `environmentpath` setting from taking
   effect.
 * Stubbed out the automatic confines created by resource providers on their
   specified commands, which was preventing the correct provider from being
   assigned to a resource when performing cross-platform testing.

## [2.6.3]

### Fixed

 * Facts derived from the node name now only get merged on top of the facts
   specified by `RSpec.configuration.default_facts` and `let(:facts)` if the
   node name has been manually specified with `let(:node)`.

## [2.6.2]

### Changed

 * Puppet 5.0.x added to the CI test matrices.
 * The automatic setup code now checks for the presence of `metadata.json` in
   the working directory. If not present, it assumes that rspec-puppet is
   running from inside a control repo instead of a module and skips creating
   the `spec/fixtures` directory structure and link.

### Added

 * A new configuration option has been added
   (`RSpec.configuration.setup_fixtures`) that controls whether rspec-puppet
   will manage the `spec/fixtures` link.

### Fixed

 * A race condition when running rspec-puppet under parallel\_tests causing
   errors when creating the `spec/fixtures` link.
 * The contents of the `networking` fact hash is no longer cleared when merging
   in the facts derived from the node name.

## [2.6.1]

### Fixed

 * 2.6.0 introduced a change to how resource titles are rendered in the test
   manifest which caused them to get rendered as double quoted strings. This
   caused a failure for tests of defined types that contained `$` characters
   as Puppet would try and interpolate the values in the title as variable(s).

## [2.6.0]

The Windows parity release. rspec-puppet now officially supports Windows. A lot
of work has been put in to support cross-platform tests, so that you can now
test your Windows manifests on \*nix, and your \*nix manifests on Windows.

### Changed

 * Puppet settings are now applied as application overrides, allowing users to
   call `Puppet.settings` directly to make changes to settings without them
   getting clobbered by rspec-puppet.
 * Improved support for setting up the `spec/fixtures/modules` link on Windows
   by using directory junctions instead of symlinks, removing the need for
   Administrator access.
 * When testing for the absence of a parameter on a resource, the error message
   now contains the value(s) of the parameter(s) that should be undefined.
 * When testing a defined type, the defined type being tested is no longer part
   of the coverage report.
 * The cached catalogue will now be invalidated when hiera-puppet-helper users
   change their `hiera_data` value.
 * Multiple instances of a defined type can now be tested at once by providing
   an array of strings with `let(:title)`.
 * Explicitly specifying the type of an example group (`:type => :class`) now
   takes precedence over the type inferred from the spec file's location.
 * The manifest specified in `RSpec.configuration.manifest` (path to `site.pp`
   for Puppet < 4.x) is now imported if specified on Puppet >= 4.x.
 * Puppet functions called when testing a Puppet function now get executed in
   the same scope as parent function.

### Added

 * The module is now automatically linked into `spec/fixtures/modules` at the
   start of the rspec-puppet run.
 * CI testing of PRs on Windows via Appveyor.
 * Support for setting node parameters (mocking the behaviour of an ENC or
   Puppet Enterprise Console) using `let(:node_params)`.
 * Support for injecting Puppet code at the end of the test code using
   `let(:post_condition)`.
 * Resource coverage reports for `host` specs.
 * Puppet functions that take a lambda as a parameter can now be tested by
   chaining `with_lambda` to the `run` matcher.
 * Facts and trusted facts are now available when testing Puppet functions.
 * Hiera configuration can now be specified when testing Puppet functions using
   `let(:hiera_config)`.
 * Trusted facts (`$trusted[]`) can now be specified in
   `RSpec.configuration.default_trusted_facts` or by `let(:trusted_facts)`.
 * `:default` is now a supported parameter value when passed in by
   `let(:params)`.
 * Support for testing Puppet data type aliases.

### Fixed

 * Facts generated from the node name (as set by `let(:node)`) now take
   precedence over the values specified in `RSpec.configuration.default_facts`
   or by `let(:facts)`.
 * Only fact names will now be converted to lowercase, not the fact values.
 * Matchers now support resources where the namevar has a different value to
   the title.
 * Resources created outside of the module being tested by functions like
   `create_resources` or `ensure_package` are no longer present in the coverage
   report from Puppet 4.6 onwards.
 * Guards have been put in place to prevent the possibility of rspec-puppet
   getting stuck in an infinite recursion when testing the relationships
   between resources.
 * A full `spec/spec_helper.rb` file is now written out by `rspec-puppet-init`
   to fix the `fixture_path` issue on new modules.
 * The namevar of a resources is no longer taken into account when testing the
   exact parameters of the resource with `only_with`.
 * Minimum resource coverage check for RSpec <= 3.2.
 * Resource parameters that take a hash as their value will no longer have that
   hash converted into an array.
 * Testing the value of a parameter with a Proc that returns `nil` now works as
   expected.
 * When testing Puppet functions, the function name is no longer automatically
   coverted to lowercase.
 * The value of `$::environment` is now forced to be a string as expected for
   Puppet 4.0 - 4.3.
 * app\_management is no longer enabled by rspec-puppet for Puppet >= 5.0 as it
   is already enabled by default.
 * Failing to provide parameters when testing an application now raises the
   correct exception (`ArgumentError`).
 * Ruby symbols in nested hashes or arrays are now converted into strings when
   passed in by `let(:params)`.
 * Namespaced resources are now correctly capitalised when being added to the
   resource coverage filter.

## [2.5.0]

Headline features are app management, nested hashes in params, and testing for "internal" functions.

Thanks to everyone who contributed: Leo Arnold, Matt Schuchard, and Si Wilkins

### Changed

* Updates to the README
* Improve Gemfile to work with older rubies

### Added

* Add support for app management testing
* Enable nested hashes in params
* After refactoring the function test code, puppet 4 "internal" functions can now be tested too
* Link functions and types on setup
* Increased test coverage

## [2.4.0]

This release now supports testing exported resources in the same way that normal resources in the catalog are tested. Access them in your examples using `exported_resources`. See "Testing Exported Resources" in the README for examples.

### Changed

* This release pulls out much of the version-specific code into separate classes to reduce complexity and enable easier maintenance going forward.

### Added

* Support colon-separated module_path and environmentpath values.
* Support a threshold for the code coverage test, that can fail the whole run.
* Ensure a consistent environment for all examples by adding a forced initialization of puppet before each.

### Credits

Thanks to Adrien Thebo, Arthur Gautier, Brett Gray, and Nicholas Hinds, as well as all the folks helping out on github for their contributions to this release.


## [2.3.2]

Properly fix yesterday's issue by unsharing the cache key before passing the data to puppet. This also contains a new test matrix to avoid missing a half-baked fix like yesterday.

## [2.3.1]

A quick workaround to re-enable testing with the recently released puppet 3.8.5 and the soon to be released puppet 4.3.2. See PUP-5743 for the gritty details. Upgrade to this version if you hit the "undefined method \`resource' for nil:NilClass" error.

## [2.3.0]

Rspec-puppet now supports testing custom types, `:undef` values in params, structured facts, and checks resource dependencies recursively.

The settings in `module_path` and `manifest` are now respected throughout the code base. The former default for `module_path` (`'/etc/puppet/modules'`) was dropped to avoid accidentally poisoning the test environment with unrelated code.

To reduce the maintenance overhead of boilerplate code, rspec-puppet now provides some of the code that rspec-puppet-init deployed in helper files that you can just `require` instead.

This release also reduces memory usage on bigger testsuites drastically by reducing the caching of compiled catalogs.

### Changed
- Limit the catalogue cache to 16 entries. Significant memory savings and reduced runtime were observed in testing this.
- Prevent Puppet 3's \_timestamp fact from invalidating cache.
- Extracted catalog cache from RSpec::Puppet::Support.
- Updated README to use the rspec 3 syntax, and additional explanations.
- `contain_file(...).with_content(...)` will now only show the diff and not the full contents of the file.

### Added
- Custom type testing example group and matcher.
- before/require/subscribe/notify checking now searches recursively through all dependencies. `File[a] -> File[b] -> File[c]` is now matched by `contain_file('a').that_comes_before('File[c]')`, whereas earlier versions would have missed that.
- `let(:params)` now allows `:undef` to pass a literal undef value through to the subject.
- Support structured facts with keys as symbols or strings (\#295).
- rspec-puppet-init now creates smaller files, using rspec-puppet helpers, instead of pasting code into the module.
- Added a list of related projects to the README.

### Fixed
- Fix #276: `compile.and_raise_error` now correctly considers successful compilation an error
- Puppet's `modulepath` can now contain multiple entries and rspec-puppet will configure puppet to load code from all of them
- Support running with rspec 2.99 again
- non-class resources are now covered by the coverage code
- Fix #323/MODULES-2374: autorequires checking doesn't abort on "undefined method \`[]' for nil:NilClass"
- improved documentation for hiera integration, added example spec
- document the `scope` property

### Credits

Thanks to Adrien Thebo, Alex Harvey, Brian, Dan Bode, Dominic Cleal, Javier Palacios, Jeff McCune, Jordan Moldow, Peter van Zetten, Raphaël Pinson, Simon Kohlmeyer, and Tristan Colgate for their contributions to this release.

  -- David Schmitt

## [2.2.0]
### Added
- Settings for ordering, strict_variables, stringify_facts, and trusted_node_data
- Expose the scope in function example groups

### Fixed
- rspec-puppet-init now works with Puppet 4
- Several fixes and enhancements for the `run` matcher
- Recompile the catalog when the hiera config changes

## [2.1.0] - 2015-04-21
### Added
- Puppet 4 support
- Ability to set `environment` in a let block
- Better function failure messages

### Fixed
- Filter fixtures from coverage reports
- Fix functions accidentally modifying rspec function arguments
- Restructured TravisCI matrix (NB: Puppet 2.6 is no longer tested)

## [2.0.1] - 2015-03-12
### Fixed
- Allow RSpec 2 to still be used

## [2.0.0] - 2014-12-02
### Changed
- `subject` is now a lambda to enable catching compilation failures.

### Added
- Ability to use RSpec 3
- Hiera integration
- Coverage reports
- Ability to test on the future parser
- Function tests now have a catalogue
- Add array of references support to Relationship matchers `that_requires`,
  `that_comes_before`, `that_notifies`, and `that_subscribes_to`

### Fixed
- Better error messaging and handling for parameters (`nil` and friends) and
  dependency cycles

## 1.0.1 and earlier
For changelog of versions 1.0.1 and earlier, see http://rspec-puppet.com/changelog/

[2.x]: https://github.com/rodjek/rspec-puppet/compare/v2.5.0...master
[2.5.0]: https://github.com/rodjek/rspec-puppet/compare/v2.4.0...v2.5.0
[2.4.0]: https://github.com/rodjek/rspec-puppet/compare/v2.3.2...v2.4.0
[2.3.2]: https://github.com/rodjek/rspec-puppet/compare/v2.3.1...v2.3.2
[2.3.1]: https://github.com/rodjek/rspec-puppet/compare/v2.3.0...v2.3.1
[2.3.0]: https://github.com/rodjek/rspec-puppet/compare/v2.2.0...v2.3.0
[2.2.0]: https://github.com/rodjek/rspec-puppet/compare/v2.1.0...v2.2.0
[2.1.0]: https://github.com/rodjek/rspec-puppet/compare/v2.0.1...v2.1.0
[2.0.1]: https://github.com/rodjek/rspec-puppet/compare/v2.0.0...v2.0.1
[2.0.0]: https://github.com/rodjek/rspec-puppet/compare/v1.0.1...v2.0.0