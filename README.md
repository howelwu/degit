# p_degit — straightforward project scaffolding


**p_degit** makes copies of git repositories. When you run `p_degit some-user/some-repo`, it will find the latest commit on https://github.com/some-user/some-repo and download the associated tar file to `~/.p_degit/some-user/some-repo/commithash.tar.gz` if it doesn't already exist locally. (This is much quicker than using `git clone`, because you're not downloading the entire git history.)

_Requires Node 8 or above, because `async` and `await` are the cat's pyjamas_

## Installation

```bash
npm install -g p_degit
```

## Usage

### Basics

The simplest use of p_degit is to download the master branch of a repo from GitHub to the current working directory:

```bash
p_degit user/repo

# these commands are equivalent
p_degit github:user/repo
p_degit git@github.com:user/repo
p_degit https://github.com/user/repo
```

Or you can download from GitLab and BitBucket:

```bash
# download from GitLab
p_degit gitlab:user/repo
p_degit git@gitlab.com:user/repo
p_degit https://gitlab.com/user/repo

# download from BitBucket
p_degit bitbucket:user/repo
p_degit git@bitbucket.org:user/repo
p_degit https://bitbucket.org/user/repo

# download from Sourcehut
p_degit git.sr.ht/user/repo
p_degit git@git.sr.ht:user/repo
p_degit https://git.sr.ht/user/repo
```

### Specify a tag, branch or commit

The default branch is `master`.

```bash
p_degit user/repo#dev       # branch
p_degit user/repo#v1.2.3    # release tag
p_degit user/repo#1234abcd  # commit hash
````

### Create a new folder for the project

If the second argument is omitted, the repo will be cloned to the current directory.

```bash
p_degit user/repo my-new-project
```

### Specify a subdirectory

To clone a specific subdirectory instead of the entire repo, just add it to the argument:

```bash
p_degit user/repo/subdirectory
```

### HTTPS proxying

If you have an `https_proxy` environment variable, Degit will use it.

### Private repositories

Private repos can be cloned by specifying `--mode=git` (the default is `tar`). In this mode, Degit will use `git` under the hood. It's much slower than fetching a tarball, which is why it's not the default.

Note: this clones over SSH, not HTTPS.

### See all options

```bash
p_degit --help
```

## Not supported

- Private repositories

Pull requests are very welcome!

## Wait, isn't this just `git clone --depth 1`?

A few salient differences:

- If you `git clone`, you get a `.git` folder that pertains to the project template, rather than your project. You can easily forget to re-init the repository, and end up confusing yourself
- Caching and offline support (if you already have a `.tar.gz` file for a specific commit, you don't need to fetch it again).
- Less to type (`p_degit user/repo` instead of `git clone --depth 1 git@github.com:user/repo`)
- Composability via [actions](#actions)
- Future capabilities — [interactive mode](https://github.com/Rich-Harris/p_degit/issues/4), [friendly onboarding and postinstall scripts](https://github.com/Rich-Harris/p_degit/issues/6)

## JavaScript API

You can also use p_degit inside a Node script:

```js
const p_degit = require('p_degit');

const emitter = p_degit('user/repo', {
	cache: true,
	force: true,
	verbose: true,
});

emitter.on('info', info => {
	console.log(info.message);
});

emitter.clone('path/to/dest').then(() => {
	console.log('done');
});
```

## Actions

You can manipulate repositories after they have been cloned with _actions_, specified in a `degit.json` file that lives at the top level of the working directory. Currently, there are two actions — `clone` and `remove`. Additional actions may be added in future.

### clone

```json
// degit.json
[
	{
		"action": "clone",
		"src": "user/another-repo"
	}
]
```

This will clone `user/another-repo`, preserving the contents of the existing working directory. This allows you to, say, add a new README.md or starter file to a repo that you do not control. The cloned repo can contain its own `degit.json` actions.

### remove

```json
// degit.json
[
	{
		"action": "remove",
		"files": ["LICENSE"]
	}
]
```

Remove a file at the specified path.

## See also

- [zel](https://github.com/vutran/zel) by [Vu Tran](https://twitter.com/tranvu)
- [gittar](https://github.com/lukeed/gittar) by [Luke Edwards](https://twitter.com/lukeed05)

## License

[MIT](LICENSE.md).
