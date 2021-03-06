# auth0-rules
Rules used for the Auth0 instances of Mozilla.
You can find more information about Auth0 at https://www.auth0.com

The rules are snippets of javascript code running as webtasks (https://www.webtasks.io), which modify the authentication flow of users in Auth0.

## Branches

`master`:
The master branch is used for development of rules and are auto-deployed on https://manage-dev.mozilla.auth0.com/

`production`:
/!\ The production branch uses merges from the master branch and are used for production. These are auto-deployed on https://manage.mozilla.auth0.com/

## Deployment & CI

Rules are deployed with `auth0-ci` <https://github.com/mozilla-iam/auth0-ci> after CI has completed.
For testing, this looks like this:

```
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install -r requirements.txt
$ uploader_rules.py <args>
```

## Development

How do I know which nodejs modules are available to me?

At this time Auth0 runs nodejs8. The module list that is cached inside webtasks is listed here:
https://auth0-extensions.github.io/canirequire/#rsa

### Development cycle

This is the cycle today. In the future we hopefully add CI driven tests. 
This cycle could be improved.

1. Write a rule in your local fork of the repo
2. Run `uploader_rules.py -r rules` to deploy the uncommitted rule to auth0-dev
3. Do manual testing in auth0-dev to determine if the rule does what you want
4. Iterate steps 1-3 until you have a rule that works
5. Remove the new rule from auth0-dev. This could be done by checking out 
   master (which doesn't have the rule) and again running `uploader_rules.py -r rules`
6. Push your branch to your fork and create a PR with your new rule, requesting 
   a review of the PR.
7. Someone reviews the PR, either suggesting changes or approving
8. Merge the PR
9. CI deployes the PR to auth0-dev
10. Manually test again in auth0-dev to validate that the rule works. This is 
    the stage to do more thorough testing as this is the last step before
    production deployment
11. If testing validates the rule is good, create a second PR from `master` to
    `production`, requesting review and referencing in the text of the PR the
    first PR which contains the initial review. Ideally the changes in the first
    dev PR and this prod PR will be the same and the reviewer can leverage
    the dev PR's review. If that's not the case a new thorough review would be
    needed.
12. During change window, merge PR. This may trigger CI to deploy to prod, or
    if CI isn't yet enabled in prod, manually deploy to prod
13. Test in prod to make sure everything works and rollback if it doesn't.  
