# Root Cause Analysis (RCA)

## Overview

The repository contained a Python application with a GitHub Actions CI pipeline and Docker configuration. The CI pipeline had multiple issues that prevented dependency installation, test execution, and Docker builds from completing successfully.

## Issues Found

### 1. Dependency File Name Mismatch

The repository contained the dependency file as `requirement.txt`, while the Dockerfile expected `requirements.txt`.

This caused the Docker build to fail at:

`COPY requirements.txt .`

with a file-not-found error.

### 2. Incorrect Dependency File Name in CI

The GitHub Actions workflow was using:

`pip install -r requirement.txt`

The dependency file was standardized to `requirements.txt`, so the CI workflow was updated to use the correct filename.

### 3. Pytest Application Import Failure

The CI test step initially ran:

`pytest tests/ --junitxml=results.xml`

The tests import the Flask application using:

`from app import app`

The test collection failed with:

`ModuleNotFoundError: No module named 'app'`

The test command was changed to:

`python -m pytest tests/ --junitxml=results.xml`

This runs pytest using the configured Python interpreter and correctly resolves the project root and application module.

## Changes Made

### 1. Renamed Dependency File

Renamed `requirement.txt` to `requirements.txt`.

### 2. Updated GitHub Actions Dependency Installation

Changed:

`pip install -r requirement.txt`

to:

`pip install -r requirements.txt`

### 3. Fixed Pytest Execution

Changed:

`pytest tests/ --junitxml=results.xml`

to:

`python -m pytest tests/ --junitxml=results.xml`

### 4. Verified Tests

The test suite was executed after the fixes to confirm that the application module could be imported and the tests could run successfully.

### 5. Verified Docker Build

The Docker build was tested after correcting the dependency filename.

## Root Cause

The main root causes were inconsistent dependency-file naming and an incorrect pytest execution method in the CI workflow.

The repository, Dockerfile, and CI workflow were not consistently referencing the same dependency filename, and the CI test command did not correctly resolve the application module during test collection.

## Prevention

* Use the standard `requirements.txt` filename consistently.
* Keep Dockerfile and CI dependency references synchronized.
* Run tests using the project's configured Python interpreter.
* Run CI and Docker builds before creating the pull request.
* Document CI failures and fixes through an RCA.

## Result

The dependency installation issue, pytest import failure, and Docker build issue were resolved. The CI pipeline can now install dependencies, execute the test suite, and build the Docker image successfully.
