# Heroku Monorepo Buildpack

Imagine you have a single code base, which has a few different applications within it... or at least the ability to run a few different applications. Or, maybe you're Google with your mono repo?

In any case, how do you manage this on Heroku? You don't. Heroku applications assume one repo to one application.

Enter the Monorepo buildpack, which is a copy of [heroku-buildpack-multi-procfile](https://github.com/heroku/heroku-buildpack-multi-procfile) except it moves the target path in to the root, rather than just the Procfile. This helps for ruby apps etc.

# Usage

1. Write a bunch of ~~Procfiles~~ apps and scatter them through out your code base.
2. Create a bunch of Heroku apps.
3. For each app, set `APP_BASE=relative/path/to/app/root`, and of course:
   `heroku buildpacks:add -a <app> https://github.com/lstoll/heroku-buildpack-monorepo`
4. For each app, `git push git@heroku.com:<app> master`

## APP_EXTRA — directories outside the app that the build needs (GAPartners fork)

Set `APP_EXTRA=gems` (comma- or space-separated, relative to the repo root) to carry
directories that live *beside* the app into the build root instead of discarding them —
for example a `gems/` directory holding Rails engines the app's Gemfile references with
`path:`. Each is moved into the app at the same relative path before the hoist, so it
ends up at `<build root>/gems`.

To make the same Gemfile path resolve in a plain checkout, commit a symlink at
`<APP_BASE>/gems -> ../../gems`. In a checkout the symlink resolves; here the buildpack
replaces it with the real directory. `Gemfile.lock`'s `PATH remote: gems/<engine>` is then
identical in both layouts, which frozen-mode Bundler requires.

Note: If you already have other buildpacks defined, you'll need to make sure that the heroku-buildpack-monorepo buildpack is defined first. You can do this by adding `-i 1` to the `heroku buildpacks:add` command.

# Authors

Andrew Gwozdziewycz <apg@heroku.com> and Cyril David <cyx@heroku.com> and now Lincoln Stoll <lstoll@heroku.com>
