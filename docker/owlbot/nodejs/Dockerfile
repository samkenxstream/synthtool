# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Version 0.2.0

# build from the root of this repo:
# docker build -t gcr.io/repo-automation-bots/owlbot-nodejs -f docker/owlbot/nodejs/Dockerfile .
FROM python:3.9.5-buster

WORKDIR /

###################### Install nodejs.
RUN curl -sL https://deb.nodesource.com/setup_15.x | bash -
RUN apt-get install -y nodejs

###################### Install synthtool's requirements.
COPY requirements.txt /synthtool/requirements.txt
RUN pip install -r /synthtool/requirements.txt

# Put synthtool in the PYTHONPATH so owlbot.py scripts will find it.
ENV PYTHONPATH="/synthtool"

# Tell synthtool to pull templates from this docker image instead of from
# the live repo.
ENV SYNTHTOOL_TEMPLATES="/synthtool/synthtool/gcp/templates"

# Copy synthtool.
COPY synthtool /synthtool/synthtool
COPY docker /synthtool/docker
COPY post-processor-changes.txt /post-processor-changes.txt

# Update permissions so non-root users won't see errors.
RUN find /synthtool -exec chmod a+r {} \;
RUN find /synthtool -type d -exec chmod a+x {} \;

# Install dependencies used for post processing:
# * gts/typescript are used for linting.
# * google-gax is used for compiling protos.
RUN cd /synthtool && mkdir node_modules && npm i gts@3.1.0 google-gax@2.29.1 typescript@3.9.9 \
    chalk@4.1.2 escodegen@2.0.0 espree@7.3.1 estraverse@5.2.0 glob@7.2.0 jsdoc@3.6.7 \
    minimist@1.2.5 semver@7.3.5 tmp@0.2.1 uglify-js@3.14.2

ENTRYPOINT [ "/bin/bash" ]
CMD [ "/synthtool/docker/owlbot/nodejs/entrypoint.sh" ]
