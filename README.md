# gh-pages-multibranch

This action allows you to deploy multiple branches in a single github
pages site, by using this arrangement:

```
/...default branch files...
/branch/branch1/...branch1 files...
/branch/other-branch/...other-branch files...
```

* On each push to the default branch, put the files in ``gh-pages:/``.
* On pushes to other branches, put the files in
  ``gh-pages:/branch/BRANCH_NAME/``
* It does this by extracting the old ``gh-pages`` branch content,
  removing any outdated content (the old branch or any branches that
  have been deleted since the last run), and adding in the new
  content.
* It doesn't deploy to gh-pages for you (other actions can do that),
  but it drops the remade files into the given ``directory``
  * To put this another way: The *directory* action argument contents gets
    replaced with the multiplexed gh-pages branch content.  You then
    use a different action to deploy this.

## Usage

```yaml
name: sphinx
on: [push, pull_request]  # No need to limit to certain branches
jobs:
  your-job:
    steps:
      [ checkout]
      [ place your built site in ./public ]

      - name: multipages
        uses: rkdarst/gh-pages-multibranch@main
        if: ${{ github.event_name == 'push' }}
        with:
          directory: public
      # Remember to not limite your deploy step to the default branch!
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.event_name == 'push' }}
        with:
          publish_branch: gh-pages
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: public/
          force_orphan: true
```

## See also

* https://github.com/peaceiris/actions-gh-pages - a good Github Pages
  publishing action.
