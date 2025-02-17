# Mayhem for Code: Example CI Integration

[![Mayhem for Code](https://drive.google.com/uc?export=view&id=1JXEbfCDMMwwnDaOgs5-XlPWQwZR93fv4)](http://mayhem.forallsecure.com/)

A GitHub Action walk through for using Mayhem for Code to check for reliability, performance, and security issues in your application binary (packaged as a containerized [Docker](https://docs.docker.com/get-started/overview/) image) as a part of a CI pipeline.

Visit the [mCode GitHub Action](https://github.com/ForAllSecure/mcode-action/) to get more details on integrating Mayhem into your CI pipeline!

## Example GitHub Actions Integration

In this example, we've provided two targets that will be built, fuzzed, and fixed/patched to showcase a multi-target mCode Action workflow within a CI pipeline: [Lighttpd](https://www.lighttpd.net/) version `1.4.15` and one of our `mayhem-example` targets, [c-base-executable](https://github.com/ForAllSecure/mayhem-examples/tree/main/c/base-executable/c-base-executable).

In particular, `lighttpd` version `1.4.15` was found to have vulnerabilities in the past, which were fixed in subsequent updates such as `1.4.52`. In tandem, our `c-base-executable` target has a bug that performs an `abort()` once a test case containing the string `bug` is input to the program, which we'll also fix in this example. Ultimately, we'll be using Mayhem in a CI pipeline to simulate a typical developer workflow such that we build and fuzz targets within a workflow to prove that vulnerabilities were found, and that subsequent PRs to fix these vulnerabilities is confirmed via Mayhem's regression testing (comparing crashing test cases of previous runs to current runs).

We have two branches in this repository: `main` and `fixed`.

> When executing a new workflow/pipeline using the mCode GitHub Action, the corresponding `lighttpd` and `c-base-executable` targets will be built within a Docker image, which is pushed to the GitHub Container Registry, and ingested by Mayhem to fuzz the containerized targets. This is done using a [multi-stage Docker image build](https://docs.docker.com/build/building/multi-stage/).

The `main` branch contains the following vulnerable targets:

* **lighttpd 1.4.15**:
    * [lighttpd 1.4.15 Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Dockerfile): Build instructions for setting up a containerized `lighttpd 1.4.15` application.
    * [lighttpd 1.4.15 Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Mayhemfile.lighttpd): Configuration options for the resulting `lighttpd 1.4.15` CI pipeline Mayhem run.
* **(vulnerable) c-base-executable**:
    * [c-base-executable Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Dockerfile): Build instructions for setting up a containerized (and vulnerable) `c-base-executable` application.
    * [c-base-executable Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Mayhemfile.mayhemit): Configuration options for the resulting (vulnerable) `c-base-executable` CI pipeline Mayhem run.

The `fixed` branch contains the following fixed targets:

* **lighttpd 1.4.52**:
    * [lighttpd 1.4.52 Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/fixed/mayhem/Dockerfile): Build instructions for settings up a containerized `lighttpd 1.4.52` application.
    * [lighttpd 1.4.52 Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/fixed/mayhem/Mayhemfile.lighttpd): Configuration options for the resulting `lighttpd 1.4.52` CI pipeline Mayhem run.
* **(fixed) c-base-executable**:
    * [c-base-executable Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/fixed/mayhem/Dockerfile): Build instructions for setting up a containerized (and fixed) `c-base-executable` application.
    * [c-base-executable Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/fixed/mayhem/Mayhemfile.mayhemit): Configuration options for the resulting (fixed) `c-base-executable` CI pipeline Mayhem run.

## Getting Started

1. Fork this repository and create a Mayhem account to copy and paste your account token to GitHub Secrets for your repository:

    a. Navigate to [mayhem.forallsecure.com](https://mayhem.forallsecure.com/) to register an account.

    b. Click your profile drop-down and go to *Settings* > *API Tokens* to access your account API token.

    c. Copy and paste your Mayhem token to your forked repo's [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-organization).

2. On the `main` branch, navigate to your GitHub repository `Actions` tab and execute a CI pipeline for the `main` branch (assuming this is not already done automatically). This will build and push the `lighttpd 1.4.15` and `(vulnerable) c-base-executable` containerized applications to the GitHub Container Registry and use Mayhem to fuzz the resulting Docker image. Results can be found in the `Security` tab or on the Mayhem instance itself with more details about the specific run.

    > **Note:** You may be required to set your package visibility settings to `Public` to give Mayhem permissions to ingest your Docker image from the GitHub Container Registry. Click on your package in the right-hand pane of your GitHub repository and go to *Package Settings*. Then, scroll down to *Package Visibility* and set the package to `Public`.

3. Now, switch to the `fixed` branch. Create a pull request and set the PR to merge to `main` (**for your forked repo**). The mCode GitHub Action will automatically begin building and pushing the new `lighttpd 1.4.52` and `(fixed) c-base-executable` containerized applications to the GitHub Container Registry and use Mayhem to fuzz the resulting Docker image against the previous crashing test cases for the corresponding targets via regression testing. Results can then be found in the PR or on the Mayhem server itself with more details about the specific run. In addition, issues in the `Security` tab will also display defects that have been fixed as a result of the update.

Congrats! You just integrated Mayhem in a multi-target CI pipeline for the `lighttpd` and `c-base-executable` applications! Extrapolating from this, you should now be able to incorporate the same steps to integrate Mayhem into your own CI pipeline for your custom code.

## About Us

ForAllSecure was founded with the mission to make the world’s critical software safe. The company has been applying its patented technology from over a decade of CMU research to solving the difficult challenge of making software safer. ForAllSecure has partnered with Fortune 1000 companies in aerospace, automotive and high-tech industries, as well as the US Department of Defense to integrate Mayhem into software development cycles for continuous security. Profitable and revenue-funded, the company is scaling rapidly.

* [https://forallsecure.com/](https://forallsecure.com/)
* [https://forallsecure.com/mayhem-for-code](https://forallsecure.com/mayhem-for-code)