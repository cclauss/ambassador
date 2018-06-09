Releasing Ambassador
====================

If you just want to **use** Ambassador, check out https://www.getambassador.io/! You don't need to build - much less release! - anything, and in fact you shouldn't.

If you don't work at Datawire, this document is probably not going to help you. Maybe check out [the developer guide](BUILDING.md) instead.

----

If you're still reading, you must be at Datawire. Congrats, you picked a fine place to work! To release Ambassador, you'll need credentials for our Github repos.

1. PRs will pile up on `master`. **Don't accept PRs for which CI doesn't show passing tests.**
2. Once `master` has all the release drivers, tag `master` with an RC tag, eg `0.33.0-rc1`.
   - Make sure that `CHANGELOG.md` and `index.html` are up to date!
   - Make sure that the Helm charts are up to date (use `make helm VERSION=0.33.0` or the like, then commit).
3. The RC tag will trigger CI to run a new build and new tests. It had better pass: if not, figure out why.
4. The RC build will be available as `quay.io/datawire/ambassador:0.33.0-rc1` and also as `quay.io/datawire/ambassador:0.33.0-rc-latest`. Any other testing you want to do against this image, rock on.
5. When you're happy with everything, tag `master` with a GA tag like `0.33.0` and let CI do its thing.

**Note well** that there must be at least one RC build before a GA, since the GA tag **does not** rebuild the docker images -- it retags the ones built by the RC build. This is intentional, to allow for testing to happen on the actual artifacts that will be released.

----
Updating Ambassador's Envoy
----

Ambassador currently relies on a custom Envoy build which includes the Ambassador `extauth` filter. This build lives in `https://github.com/datawire/envoy`, which is a fork of `https://github.com/envoyproxy/envoy`, and it'll need to be updated at least as often as Envoy releases happen. To do that:

1. Clone the `datawire/envoy` repo and get to the `datawire/extauth-build-automation` branch:

    ```
    git clone git@github.com:datawire/envoy.git
    cd envoy
    git checkout datawire/extauth-build-automation
    ```

2. Follow the directions in `DATAWIRE/README.md` to get everything built and tested and pushed.

3. Once the new `ambassador-envoy` image has been pushed, get back to your clone of the Ambassador repo and update the `FROM` line in `ambassador/Dockerfile` to use the new image.

4. Build and test Ambassador as normal.

