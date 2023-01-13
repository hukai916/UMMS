# Demo of Sylab Remote Builder
## Register:
1.  Sign up at https://cloud.sylabs.io/builder
2.  Create a token by following: https://docs.sylabs.io/guides/3.3/user-guide/cloud_library.html#creating-a-access-token
  - The token will be valid for 30 days.
  - Copy the token somewhere safe.

## On SCI:
1.  Token verification:
- ```
  singularity remote login
  ```
2. Build a tiny image that contains Miniconda3:
- Copy the following recipe into `miniconda3.def`:
  - ```
    Bootstrap: docker
    From: continuumio/miniconda3:22.11.1

    %labels
        Author List
    ```
- Use the Remote Builder:
  - ```
    singularity build --remote miniconda3.sif miniconda3.def
    ```
- Push the built `miniconda3.sif` to the Library Registry hosted by Sylab:
  - replace **hukai916_singularity** with your username, and
  - ```
    singularity push -U miniconda3.sif library://hukai916_singularity/miniconda3/miniconda3:0.1
    ```
- Now, others can pull it from the Library:
  - ```
    singularity pull library://hukai916_singularity/miniconda3/miniconda3:0.1
    ```
  - Or simply:
  - ```
    singularity pull hukai916_singularity/miniconda3/miniconda3:0.1
    ```

## Interactive testing example:
1.  Use the following recipe to create an image with base R 4.2.2 (leveraging the newly created `library://hukai916_singularity/miniconda3/miniconda3:0.1` as base OS):
- Copy the following recipe to `r.def`: replace `hukai916_singularity` with your username
  - ```
    Bootstrap: library
    From: hukai916_singularity/miniconda3/miniconda3:0.1

    %post
        # install base R with conda
        conda install -c conda-forge r-base=4.2.2
    ```
- Then, build and push to Library:
  - ```
    singularity build --remote r.sif r.def
    singularity push -U r.sif library://hukai916_singularity/r/r:0.1
    ```
2.  Now, test the `r.sif` image by installing some R packages:
- ```
  singularity shell r.def
  ```
Then, invoke `R`, and:
- ```
  install.packages("BiocManager")
  BiocManager::install("RCurl")
  ```
Frustratingly, we find the `RCurl` failed to install due to:
- ```
  configure: error: in `/tmp/RtmpautzLB/R.INSTALL1bbb54569abf26/RCurl':
  configure: error: no acceptable C compiler found in $PATH
  See `config.log' for more details
  ERROR: configuration failed for package ‘RCurl’
  ```
Seems that the `C compiler` system dependency is missing.
3.  Interactively testing by creating a 'sister' Docker image on your local machine:
- Copy the following Docker recipe file into `docker_sister/Dockerfile` and build a Docker image:
  - ```
    FROM continuumio/miniconda3:22.11.1

    RUN conda install -c conda-forge r-base=4.2.2
    ```
- Then run (replace `hukai916` with your Docker Hub id):
  - ```
    cd docker_sister
    docker build -t hukai916/r:0.1 .
    ```
- Interactively replicate and test the `RCurl` failed to install issue:
  - ```
    docker run -it hukai916/r:0.1
    ```
  - Invoke `R`, and:
  - ```
    install.packages("BiocManager")
    BiocManager::install("RCurl")
    ```
  - We can replicate the same error message, a quick Google search found the following solution: https://stackoverflow.com/questions/19816275/no-acceptable-c-compiler-found-in-path-when-installing-python, seems that we need to install `build-essential` package. Let's do it, first quit R by `q()`, then:
  - ```
    apt-get update -q && apt-get install build-essential
    ```
  - After installing `build-essential`, the `RCurl` package can be successfully installed.
-  Now, copy the working solution to your `Singularity` recipe:
  - Save the updated recipe below into `r_0.2.def`, of note, this time, we use our previously built `library://hukai916_singularity/r/r:0.1` as our base OS so that we do not need to install base R again
  - ```
    Bootstrap: library
    From: hukai916_singularity/r/r:0.1

    %post
        # install the missing build-essential package required for RCurl
        apt-get update -q && apt-get install -q -y build-essential
    ```
  - Build and try install `RCurl` again:
  - ```
    singularity build --remote r_0.2.sif r_0.2.def
    ```
  - Invoke `R`, and:
  - ```
    BiocManager::install("RCurl")
    ```
  - Viola, `RCurl` can be successfully installed now!
  - Finally, you can push this version to the Library Registry:
  - ```
    singularity push -U r_0.2.sif library://hukai916_singularity/r/r:0.2
    ```

## Side notes
### 1. Different R packages may depend on different system libraries
- Different R packages may depend on different system libraries, below is a list of commonly required system libraries:
  - ```
    build-essential:      required by many
    libxml2-dev:          required by XML
    libssl-dev:           required by rtracklayer
    libjpeg-turbo8-dev:   required by ShortRead
    libpng-dev:           required by ShortRead
    libbz2-dev:           required by ShortRead
    liblzma-dev:          required by ShortRead
    libcurl4-openssl-dev: required by ShortRead
    ```
- Also check out this list:
  - https://github.com/rocker-org/r-devel-san-clang/blob/608296d6833d2e03e44409c557cfb2990cb5223b/Dockerfile#L19-L69
