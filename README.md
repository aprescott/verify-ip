# verify-ip

Given access log request lines on STDIN, returns only those from an IP that reverse-lookups to a domain matching the one specified.

An example use case is filtering out web server access log lines that come only from valid Google bot requests, to avoid a spoofed user agent string, for example:

```bash
cat access.log |
  grep -E 'Feedfetcher-Google[^"]+"$' |
  verify-ip --domain 'google\.com'
```

Comes with a convenience method for filtering out all Google _web crawler_ (does not include Feedfetcher) requests:

```bash
verify-ip --google
```

# License and contributing

Copyright (c) 2013 Adam Prescott <https://aprescott.com/>.

verify-ip is released under the MIT license. See LICENSE for details.

Any contributions will be assumed by default to be under the same terms.

The quickest way to get changes contributed:

1. Visit the [GitHub repository](https://github.com/aprescott/verify-ip).
2. [Fork the repository](https://help.github.com/articles/fork-a-repo).
3. Check out a branch on the latest master for your change: `git checkout -b master new-feature` --- do not make changes on `master`!
4. [Send a pull request on GitHub](https://help.github.com/articles/fork-a-repo), including a description of what you've changed. (Note: your contribution will be assumed to be under the same terms of the project by default.)

# Help & usage

```
Synopsis:

    verify-ip --domain[-pattern] REGEX_PATTERN
              [--google]
              [-h | --help]

Options:

    -h, --help

    	Print the help page and exit.

    --domain[-pattern] REGEX_PATTERN

        When the IP specified by --ip is put through a reverse look-up,
        only treat it as a "valid" IP if the domain found matches
        REGEX_PATTERN. Note that REGEX_PATTERN will be used as,

            (^|\.?)${REGEX_PATTERN}\.$

        to ensure a fully-qualified domain so that, e.g.,
        "googlebot.com.fakedomain.com." does not match "googlebot\.com",
        and "myfakegooglebot.com." does not match "googlebot\.com\.$".

    --google

        Optional.

        Pre-filters lines that match only known Google web crawler user
        agent string fragments, such as "Mediapartners-Google" and Assumes
        --domain is passed with "googlebot\.com".

        Assumes that the user agent strings do not contain any "
        characters and appear at the end of the line, as with
        the combined log format.

Notes:

    All lines are assumed to contain an IP address as the first space-
    delimited token.

Examples:

    Simple usage:
  
        cat access.log | verify-ip --domain "foo\.com"

    Filter Google web crawler requests by UA + IP:

        cat access.log | verify-ip --google

    Filter valid Google requests only for AdsBot-Google
    requests, based on a UA string from a combined logging
    format:

        cat access.log | grep -E 'AdsBot-Google[^"]+"$' |
          verify-ip --domain 'googlebot\.com'

    Filter only Google Reader requests that come from Feedfetcher,
    which comes from the "google.com" domain, as per
    http://support.google.com/webmasters/bin/answer.py?hl=en&answer=182072 :

        cat access.log | grep -E 'Feedfetcher-Google' |
          verify-ip --domain "google\.com"

```