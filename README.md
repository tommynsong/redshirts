# Redshirts

Counts contributors for git repositories in the same way that Prisma Cloud Code Security counts them for developer-based pricing. Use this tool to estimate the impact on credit consumption prior to connecting repos to the platform. Note that while this tool applies the same logic as the platform when identifying users, due to the timing of platform scans, differences in repo visibility for different access tokens, etc, these results may not exactly match those in the platform. Report issues to your account team, PANW support, or at https://github.com/bridgecrewio/redshirts/issues

<!-- toc -->

-   [Redshirts](#redshirts)
-   [Usage](#usage)
-   [Commands](#commands)
<!-- tocstop -->

# Usage

<!-- usage -->

```sh-session
$ npm install -g redshirts
$ redshirts COMMAND
running command...
$ redshirts (--version)
redshirts/0.0.0 darwin-arm64 node-v16.17.0
$ redshirts --help [COMMAND]
USAGE
  $ redshirts COMMAND
...
```

<!-- usagestop -->

# Commands

<!-- commands -->

-   [`redshirts azuredevops`](#redshirts-azuredevops)
-   [`redshirts bitbucket`](#redshirts-bitbucket)
-   [`redshirts bitbucket-server`](#redshirts-bitbucket-server)
-   [`redshirts github`](#redshirts-github)
-   [`redshirts github-server`](#redshirts-github-server)
-   [`redshirts gitlab`](#redshirts-gitlab)
-   [`redshirts gitlab-server`](#redshirts-gitlab-server)
-   [`redshirts help [COMMAND]`](#redshirts-help-command)
-   [`redshirts local`](#redshirts-local)

## `redshirts azuredevops`

Count active contributors for Azure DevOps repos

```
USAGE
  $ redshirts azuredevops -t <value> [--orgs <value>] [--projects <value>] [--skip-projects <value>] [-o
    summary|json|csv] [--ca-cert <value>] [-d <value>] [--sort repo|contributors] [--exclude-empty] [--include-public]
    [--log-level error|warn|info|debug] [--repos <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file
    <value>]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --orgs=<value>
      Org names for which to fetch repos. Use the --repos and / or --projects options to add additional specific repos on
      top of those in the specified org(s). Use the --skip-repos and / or --skip-projects options to exclude individual
      repos or projects that are a part of these org(s).

  --projects=<value>
      Project names for which to fetch repos. Use the --repos option to add additional specific repos on top of those in
      the specified project(s). Use the --skip-repos option to exclude individual repos that are a part of these
      project(s).

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-projects=<value>
      Project names for which to skip fetching repos. Use this option to skip projects that are part of the specified
      orgs. If the same project is included in both --projects and --skip-projects, it is skipped. If a repo from a
      skipped project is included in --repos, it is also still skipped.

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>  (required) An Azure DevOps user personal access token tied to the provided username. This token
                       must be tied to a user that has sufficient visibility of the repo(s) being counted.

CONNECTION FLAGS
  --ca-cert=<value>  Path to certificate chain to use in HTTP requests. See
                     https://www.baeldung.com/linux/ssl-certificates for more information on obtaining a certificate
                     chain for your environment. Note that for some systems, the hostname for the API endpoint may be
                     different from the hostname you visit in a browser (e.g., api.github.com vs. github.com). Be sure
                     to obtain a certificate for the correct hostname.

DESCRIPTION
  Count active contributors for Azure DevOps repos

  Note: you must provide --repos, --projects, and / or --orgs. Due to limitations in Azure DevOps APIs, it is not
  possible to use a personal access token to fetch all orgs and repos for a user.

  About rate limiting: For Azure DevOps, this tool will attempt to submit requests in a burst until a rate limit is hit,
  and then respect the rate limit reset information provided in the response. Azure DevOps does not consistently provide
  rate limit headers in the responses, and thus it is not possible to always avoid hitting a rate limit.

EXAMPLES
  $ redshirts azuredevops --token obnwxxx --repos org/project/repo,org/project/repo2,org/project2/repo

  $ redshirts azuredevops --token obnwxxx --orgs bridgecrewio,try-bridgecrew

  $ redshirts azuredevops --token obnwxxx --orgs bridgecrewio,try-bridgecrew --projects org/project
```

_See code: [dist/commands/azuredevops.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/azuredevops.ts)_

## `redshirts bitbucket`

Count active contributors for Bitbucket repos

```
USAGE
  $ redshirts bitbucket -u <value> -t <value> [--workspaces <value>] [-o summary|json|csv] [--ca-cert <value>]
    [-d <value>] [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level error|warn|info|debug]
    [--repos <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>] [--requests-per-hour
    <value>]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

  --workspaces=<value>
      A comma separated list of workspace and / or usernames for which to fetch repos. Use the --repos option to add
      additional specific repos on top of those in the specified workspace(s). Use the --skip-repos option to exclude
      individual repos that are a part of these workspace(s).

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>     (required) A Bitbucket app token tied to the provided username. This token must be tied to a
                          user that has sufficient visibility of the repo(s) being counted.
  -u, --username=<value>  (required) Your Bitbucket username associated with the provided app token

CONNECTION FLAGS
  --ca-cert=<value>
      Path to certificate chain to use in HTTP requests. See https://www.baeldung.com/linux/ssl-certificates for more
      information on obtaining a certificate chain for your environment. Note that for some systems, the hostname for the
      API endpoint may be different from the hostname you visit in a browser (e.g., api.github.com vs. github.com). Be
      sure to obtain a certificate for the correct hostname.

  --requests-per-hour=<value>
      [default: 1000] The maximum number of requests to the server per hour. Requests will be throttled and spread to
      achieve this limit. For example, if you specify a value of 3600 here, approximately one request per second will be
      submitted. Use this to adjust the throttling of the API calls in the event that the rate limit is being consumed by
      other sources simultaneously. If you are running in a self-hosted server environment without API rate limits, you
      can also set this to a very high number to effectively disable throttling, but this may impact server performance.

DESCRIPTION
  Count active contributors for Bitbucket repos

  About rate limiting: Bitbucket uses an hourly rate limit that rolls over every minute. Thus, this tool will attempt to
  submit requests in as much of a burst as possible while respecting the rolling limit. If you run this tool multiple
  times in quick succession, or if there are other external consumers of this rate limit, you may need to provide a
  lower value here, because there is no way to check the rate liit status in a stateless way. For Bitbucket, you can
  also control throttling by setting the MAX_REQUESTS_PER_SECOND environment variable. This will cause the tool to
  submit no more than that many requests per second from the start of execution. This will slow down execution but avoid
  unexpected rate limit issues.

EXAMPLES
  $ redshirts bitbucket --username my_username --token ATBBXXX --repos bridgecrewio/checkov,try-bridgecrew/terragoat

  $ redshirts bitbucket --username my_username --token ATBBXXX --workspaces bridgecrewio,try-bridgecrew
```

_See code: [dist/commands/bitbucket.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/bitbucket.ts)_

## `redshirts bitbucket-server`

Count active contributors for Bitbucket server (self-hosted) repos

```
USAGE
  $ redshirts bitbucket-server -u <value> -t <value> -h <value> [--workspaces <value>] [-o summary|json|csv]
    [--ca-cert <value>] [-d <value>] [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level
    error|warn|info|debug] [--repos <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>]
    [--requests-per-hour <value>] [-p <value>] [--protocol http|https] [--projects <value>]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --projects=<value>
      A comma separated list of projects and / or usernames for which to fetch repos. Use the --repos option to add
      additional specific repos on top of those in the specified project(s). Use the --skip-repos option to exclude
      individual repos that are a part of these project(s). For users, use the value you would use in the git clone URL:
      `~username`.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

  --workspaces=<value>
      A comma separated list of workspace and / or usernames for which to fetch repos. Use the --repos option to add
      additional specific repos on top of those in the specified workspace(s). Use the --skip-repos option to exclude
      individual repos that are a part of these workspace(s).

CONNECTION FLAGS
  -h, --hostname=<value>
      (required) The hostname of your server, e.g. `git.mycompany.com`. Do not include the port and protocol here (see
      --port and --protocol).

  -p, --port=<value>
      The port of your server, if not a standard port (443 for https, or 80 for http).

  --ca-cert=<value>
      Path to certificate chain to use in HTTP requests. See https://www.baeldung.com/linux/ssl-certificates for more
      information on obtaining a certificate chain for your environment. Note that for some systems, the hostname for the
      API endpoint may be different from the hostname you visit in a browser (e.g., api.github.com vs. github.com). Be
      sure to obtain a certificate for the correct hostname.

  --protocol=(http|https)
      [default: https] Protocol for your server, https (default) or http. Affects the default port value if you do not
      specify a port. You must specify a protocol here, not in the hostname.

  --requests-per-hour=<value>
      [default: 1000] The maximum number of requests to the server per hour. Requests will be throttled and spread to
      achieve this limit. For example, if you specify a value of 3600 here, approximately one request per second will be
      submitted. Use this to adjust the throttling of the API calls in the event that the rate limit is being consumed by
      other sources simultaneously. If you are running in a self-hosted server environment without API rate limits, you
      can also set this to a very high number to effectively disable throttling, but this may impact server performance.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>     (required) A Bitbucket app token tied to the provided username. This token must be tied to a
                          user that has sufficient visibility of the repo(s) being counted.
  -u, --username=<value>  (required) Your Bitbucket username associated with the provided app token

DESCRIPTION
  Count active contributors for Bitbucket server (self-hosted) repos

  This tool works with Bitbucket server v1 APIs.

  About rate limiting: Bitbucket server rate limiting is unique in that you specify a "token bucket size" and "refill
  rate". To translate this to requests per hour, you must calculate how many requests a client can submit in an hour
  without being limited. This is basically equal to the refill rate, which is the number of requests per second we can
  submit indefinitely without being limited. The requests per hour is then the refill rate * 3600.
  See https://confluence.atlassian.com/bitbucketserver/improving-instance-stability-with-rate-limiting-976171954.html
  for more information.

  If you run this tool multiple times in quick succession, or if there are other external consumers of this rate limit,
  you may need to provide a lower value here, because there is no way to check the rate liit status in a stateless way.
  For Bitbucket Server, you can also control throttling by setting the MAX_REQUESTS_PER_SECOND environment variable.
  This will cause the tool to submit no more than that many requests per second from the start of execution. This will
  slow down execution but avoid unexpected rate limit issues.

EXAMPLES
  $ redshirts bitbucket-server --token ATXXX --repos bridgecrewio/checkov,try-bridgecrew/terragoat --hostname github.mycompany.internal

  $ redshirts bitbucket-server --token ATXXX --workspaces bridgecrewio,try-bridgecrew --hostname github.mycompany.internal --port 9999
```

_See code: [dist/commands/bitbucket-server.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/bitbucket-server.ts)_

## `redshirts github`

Count active contributors for GitHub repos

```
USAGE
  $ redshirts github -t <value> [--orgs <value>] [-o summary|json|csv] [--ca-cert <value>] [-d <value>]
    [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level error|warn|info|debug] [--repos
    <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --orgs=<value>
      Organization names and / or usernames for which to fetch repos. Note: due to limitations in the GitHub APIs,
      specifying a username here will only result in the user's public repositories being included. Use the --repos option
      to add additional specific repos on top of those in the specified users / orgs (including user private repos, if
      authorized). Use the --skip-repos option to exclude individual repos that are a part of these users / orgs.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>  (required) Github personal access token. This token must be tied to a user that has sufficient
                       visibility of the repo(s) being counted.

CONNECTION FLAGS
  --ca-cert=<value>  Path to certificate chain to use in HTTP requests. See
                     https://www.baeldung.com/linux/ssl-certificates for more information on obtaining a certificate
                     chain for your environment. Note that for some systems, the hostname for the API endpoint may be
                     different from the hostname you visit in a browser (e.g., api.github.com vs. github.com). Be sure
                     to obtain a certificate for the correct hostname.

DESCRIPTION
  Count active contributors for GitHub repos

EXAMPLES
  $ redshirts github --token ghp_xxxx --repos bridgecrewio/checkov,try-bridgecrew/terragoat

  $ redshirts github --token ghp_xxxx --orgs bridgecrewio,try-bridgecrew
```

_See code: [dist/commands/github.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/github.ts)_

## `redshirts github-server`

Count active contributors for GitHub server (self-hosted) repos

```
USAGE
  $ redshirts github-server -t <value> -h <value> [--orgs <value>] [-o summary|json|csv] [--ca-cert <value>] [-d
    <value>] [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level error|warn|info|debug]
    [--repos <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>] [-p <value>] [--protocol
    http|https]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --orgs=<value>
      Organization names and / or usernames for which to fetch repos. Note: due to limitations in the GitHub APIs,
      specifying a username here will only result in the user's public repositories being included. Use the --repos option
      to add additional specific repos on top of those in the specified users / orgs (including user private repos, if
      authorized). Use the --skip-repos option to exclude individual repos that are a part of these users / orgs.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

CONNECTION FLAGS
  -h, --hostname=<value>   (required) The hostname of your server, e.g. `git.mycompany.com`. Do not include the port and
                           protocol here (see --port and --protocol).
  -p, --port=<value>       The port of your server, if not a standard port (443 for https, or 80 for http).
  --ca-cert=<value>        Path to certificate chain to use in HTTP requests. See
                           https://www.baeldung.com/linux/ssl-certificates for more information on obtaining a
                           certificate chain for your environment. Note that for some systems, the hostname for the API
                           endpoint may be different from the hostname you visit in a browser (e.g., api.github.com vs.
                           github.com). Be sure to obtain a certificate for the correct hostname.
  --protocol=(http|https)  [default: https] Protocol for your server, https (default) or http. Affects the default port
                           value if you do not specify a port. You must specify a protocol here, not in the hostname.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>  (required) Github personal access token. This token must be tied to a user that has sufficient
                       visibility of the repo(s) being counted.

DESCRIPTION
  Count active contributors for GitHub server (self-hosted) repos

  This tool works with GitHub enterprise server v3 APIs. Note that earlier versions are out of support from GitHub, and
  thus are not supported here.

  About rate limiting: GitHub server returns rate limit details in response headers, and thus this tool will submit
  requests as quickly as possible while respecting the rate limit provided. If rate limiting is disabled on the server,
  then this tool will not attempt to throttle requests.

EXAMPLES
  $ redshirts github-server --token ghp_xxxx --repos bridgecrewio/checkov,try-bridgecrew/terragoat --hostname github.mycompany.internal

  $ redshirts github-server --token ghp_xxxx --orgs bridgecrewio,try-bridgecrew --hostname github.mycompany.internal --port 9999
```

_See code: [dist/commands/github-server.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/github-server.ts)_

## `redshirts gitlab`

Count active contributors for GitLab repos

```
USAGE
  $ redshirts gitlab -t <value> [--groups <value>] [-o summary|json|csv] [--ca-cert <value>] [-d <value>]
    [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level error|warn|info|debug] [--repos
    <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --groups=<value>
      Group names and / or usernames for which to fetch repos. These values must be the namespace slug, as it appears in
      GitLab URLs, not the display name, which might be different. For groups, includes all subgroups. For users, this
      will only include repos owned by that user. Use the --repos option to add additional specific repos on top of those
      in the specified group(s). Use the --skip-repos option to exclude individual repos that are a part of these
      group(s).

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>  (required) Gitlab personal access token. This token must be tied to a user that has sufficient
                       visibility of the repo(s) being counted.

CONNECTION FLAGS
  --ca-cert=<value>  Path to certificate chain to use in HTTP requests. See
                     https://www.baeldung.com/linux/ssl-certificates for more information on obtaining a certificate
                     chain for your environment. Note that for some systems, the hostname for the API endpoint may be
                     different from the hostname you visit in a browser (e.g., api.github.com vs. github.com). Be sure
                     to obtain a certificate for the correct hostname.

DESCRIPTION
  Count active contributors for GitLab repos

  About rate limiting: For GitLab, this tool will attempt to submit requests in a burst until a rate limit is hit, and
  then respect the rate limit reset information provided in the response. GitLab does not consistently provide rate
  limit headers in the responses, and thus it is not possible to always avoid hitting a rate limit.

EXAMPLES
  $ redshirts gitlab --token glpat_xxxx --repos bridgecrewio/checkov,group/subgroup/terragoat

  $ redshirts gitlab --token glpat_xxxx --groups bridgecrewio,try-bridgecrew,group/subgroup
```

_See code: [dist/commands/gitlab.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/gitlab.ts)_

## `redshirts gitlab-server`

Count active contributors for GitLab server (self-hosted) repos

```
USAGE
  $ redshirts gitlab-server -t <value> -h <value> [--groups <value>] [-o summary|json|csv] [--ca-cert <value>] [-d
    <value>] [--sort repo|contributors] [--exclude-empty] [--include-public] [--log-level error|warn|info|debug]
    [--repos <value>] [--repo-file <value>] [--skip-repos <value>] [--skip-repo-file <value>] [-p <value>] [--protocol
    http|https]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --groups=<value>
      Group names and / or usernames for which to fetch repos. These values must be the namespace slug, as it appears in
      GitLab URLs, not the display name, which might be different. For groups, includes all subgroups. For users, this
      will only include repos owned by that user. Use the --repos option to add additional specific repos on top of those
      in the specified group(s). Use the --skip-repos option to exclude individual repos that are a part of these
      group(s).

  --include-public
      The platform only counts contributors in private repos (and "internal" repos for some enterprise systems). If you
      wish to see contributors in public repos, for informational or other purposes, use this flag. This will also cause
      Redshirts to skip checking if a repo is public, so it can speed up the runtime if you know you are only supplying
      private repos, or mostly private repos, using --repos, --orgs, etc.

  --repo-file=<value>
      The name of a file containing a list of repositories to fetch, one per line. See the --repos option for more
      details.

  --repos=<value>
      A comma-separated list of repository names to fetch, as fully qualified paths (e.g., owner/repo,
      group/subgroup/project, etc). For systems where the repo display name can differ from the repo URL / slug (e.g.,
      Gitlab), the repo slug must be used. Takes precedence over --repos-file. If no repo or org / group list is provided,
      then the script will run over all repos for the token provided. Note that for some systems, this is not equivalent
      to a list of all possible repos. See the notes for each VCS for more information. If used with --orgs, --groups,
      etc, then this is a list of *additional* repos to fetch beyond the repos associated with the specified org(s) /
      group(s).

  --skip-repo-file=<value>
      The name of a file containing repository names to skip, one per line. See --skip-repos for more details.

  --skip-repos=<value>
      A comma-separated list of repository names to skip - used in conjunction with the VCS --orgs, --groups, etc options
      to skip specific repos within those groupings, or to filter out repos if no repo / org list is specified at all. If
      the same repo is included in --repos and --skip-repos, then the repo will be skipped. Takes precedence over
      --skip-repo-file.

CONNECTION FLAGS
  -h, --hostname=<value>   (required) The hostname of your server, e.g. `git.mycompany.com`. Do not include the port and
                           protocol here (see --port and --protocol).
  -p, --port=<value>       The port of your server, if not a standard port (443 for https, or 80 for http).
  --ca-cert=<value>        Path to certificate chain to use in HTTP requests. See
                           https://www.baeldung.com/linux/ssl-certificates for more information on obtaining a
                           certificate chain for your environment. Note that for some systems, the hostname for the API
                           endpoint may be different from the hostname you visit in a browser (e.g., api.github.com vs.
                           github.com). Be sure to obtain a certificate for the correct hostname.
  --protocol=(http|https)  [default: https] Protocol for your server, https (default) or http. Affects the default port
                           value if you do not specify a port. You must specify a protocol here, not in the hostname.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

AUTHENTICATION FLAGS
  -t, --token=<value>  (required) Gitlab personal access token. This token must be tied to a user that has sufficient
                       visibility of the repo(s) being counted.

DESCRIPTION
  Count active contributors for GitLab server (self-hosted) repos

  This tool works with GitLab enterprise server v4 APIs, supported by server versions 13.x and higher. Note that earlier
  versions are out of support from GitLab, and thus are not supported here.

EXAMPLES
  $ redshirts gitlab-server --token ghp_xxxx --repos bridgecrewio/checkov,try-bridgecrew/terragoat --hostname gitlab.mycompany.internal

  $ redshirts gitlab-server --token ghp_xxxx --groups bridgecrewio,try-bridgecrew,group/subgroup --hostname gitlab.mycompany.internal --port 9999
```

_See code: [dist/commands/gitlab-server.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/gitlab-server.ts)_

## `redshirts help [COMMAND]`

Display help for redshirts.

```
USAGE
  $ redshirts help [COMMAND] [-n]

ARGUMENTS
  COMMAND  Command to show help for.

FLAGS
  -n, --nested-commands  Include all nested commands in the output.

DESCRIPTION
  Display help for redshirts.
```

_See code: [@oclif/plugin-help](https://github.com/oclif/plugin-help/blob/v5.1.19/src/commands/help.ts)_

## `redshirts local`

Count active contributors in local directories using `git log`

```
USAGE
  $ redshirts local [--directories <value>] [--directory-file <value>] [--skip-directories <value>]
    [--skip-directory-file <value>] [-o summary|json|csv] [-d <value>] [--sort repo|contributors] [--exclude-empty]
    [--log-level error|warn|info|debug]

REPO SPECIFICATION FLAGS
  -d, --days=<value>
      [default: 90] The number of days for which to fetch commit history. Defaults to 90, which is the value used in the
      Prisma Cloud platform. It is not recommended to change this except for experimentation purposes.

  --directories=<value>
      (One of --directories or --directory-file is required.) A comma-separated list of relative or absolute paths to
      directories on the file system that contain git repositories. The tool will traverse this directory recursively,
      stopping at any directories with a .git directory (the root of a repo) - it will not traverse more deeply than that.
      This means that if you have a repo with a submodule, you should specify the submodule directory explicitly here. If
      you have directories with commas in the name, use the --directory-file option.

  --directory-file=<value>
      (One of --directories or --directory-file is required.) A file containing a list of directories to scan, one per
      line. See --directories for more details.

  --skip-directories=<value>
      A comma-separated list of relative or absolute paths of directories within the directories of --directories to skip.
      Relative paths are based on the directory from which you run the tool, not relative to the directories in the
      --directory list. It is recommended to use absolute paths for skipping directories. Once any skipped directory is
      reached, traversing of that part of the directory tree immediately stops.

  --skip-directory-file=<value>
      A file containing a list of directories to scan, one per line. See --skip-directories for more details.

OUTPUT FLAGS
  -o, --output=(summary|json|csv)
      [default: summary] Output format for displaying data. Defaults to 'summary', which is a console-friendly tabular
      output. To see full committer details, instead of just the unique count (total and per repo), use 'json'.

  --exclude-empty
      Do not include repos with no commits in the output

  --log-level=(error|warn|info|debug)
      [default: warn] Set the log level for the execution. Can also be set with the LOG_LEVEL environment variable. Use
      'debug' for granular logging, which will be required for any support cases. You can log individual responses using
      the environment variable LOG_API_RESPONSES=true. You can also disable all logging by setting DISABLE_LOGS=true as an
      environment variable. This is not recommended, as you may miss important processing messages. All logs will be
      written to the stderr stream.

  --sort=(repo|contributors)
      [default: repo] The output field on which to sort for CSV or console output: alphabetically by repo fully qualified
      name, or by descending contributor count (ignored for JSON)

DESCRIPTION
  Count active contributors in local directories using `git log`

  Note that `local` mode has no way to determine if a repo is public or private. Thus, all repos will be counted, and
  this may cause different behavior in the platform, which does not include public repos in the contributor count.

EXAMPLES
  $ redshirts local --repos . --skip-repos ./terragoat

  $ redshirts local --repos ~/repos,/tmp/repo,/tmp/repo/submodule
```

_See code: [dist/commands/local.ts](https://github.com/bridgecrewio/redshirts/blob/v0.0.0/dist/commands/local.ts)_

<!-- commandsstop -->
