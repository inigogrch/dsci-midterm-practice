# dsci-midterm-practice

# LOCAL TEST i wanna make this edit:
How to run the script:
1. build the container: `docker build -t mycontainer`
2. run the container: `docker run -p 8787:8787 -e PASSWORD="password" mycontainer`
3. go into rstudio: open a browser, go to `localhost:8787`
4. open the terminal tab
5. run `bash/home/rstudio/list.sh`

# REMOTE TEST:
FROM rocker/rstudio:4.4.2

USER root

# Install renv
RUN Rscript -e "install.packages('renv', repos = 'https://cloud.r-project.org')"

# Install crayon (to demonstrate GitHub Actions workflow is functional)
RUN Rscript -e "install.packages('crayon', repos = 'https://cloud.r-project.org')"

# Copy just the lockfile first (and only re-restore if this changes)
COPY renv.lock /home/rstudio/project/renv.lock

WORKDIR /home/rstudio/project
RUN chown -R rstudio:rstudio /home/rstudio/project

USER rstudio

# Restore dependencies (cached if renv.lock hasn't changed)
RUN Rscript -e "renv::restore()"

# Switch back to root, copy the rest of the renv files in the project
USER root
COPY . /home/rstudio/project
RUN chown -R rstudio:rstudio /home/rstudio/project
