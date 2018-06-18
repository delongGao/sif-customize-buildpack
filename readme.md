# Heroku Buildpack: alignment programs

Build alignment algorithm binaries on heroku so that we could run them from the main alignment app. Based on this repo: [link](https://github.com/ryandotsmith/null-buildpack).

## Basics on Custom Heroku Buildpacks

- Heroku documentation on buildpacks: [link](https://devcenter.heroku.com/articles/buildpacks).
- A detailed blog about how to use custom buildpacks: [link](https://blog.heroku.com/hacking-buildpacks).
  - __Note__: there is one outdated info in this post about setting `$PATH` environment variable.
    - Setting up `$PATH` from `release` script using `config_vals` is no longer supported.
    - Using `profile.d` scripts is the new recommended way: [link](https://devcenter.heroku.com/articles/buildpack-api#profile-d-scripts)

## Approach

- Currently supporting 3 main algorithms:
  - `MUSCLE`: [link](http://drive5.com/muscle/)
  - `ClustalW`: [link](http://www.clustal.org/clustal2/)
  - `ClustalOmega`: [link](http://www.clustal.org/omega/)
- They all have pre-compiled binaries for major platforms, we download the ones for:
  - __Mac-64__: for local dev environment
  - __linux-64__: for server
- Then we do some pre-processings to change the file names and compress files. TODO: add script here
- Upload the processed `tar.gz` files to Amazon S3, under the bucket: `sif-sprint/alignment_algorithms` and __make them publically available__ (__NOTE__: this is required, since custom buildpack need to have access to download them, also there is really no confidential data in them.)
- During app building process, at [the compile step](./bin/compile), these binaries will be downloaded from S3, stored in the correct folder(`vendor`) with correct names. At the end of compilation, we append `vendor` folder to the `$PATH` environment variable.
