# Serverless Containers on Fargate

Slides of a talk about running serverless containers in Amazon Fargate

## slides
For the slides we use the [RevealJS](https://github.com/hakimel/reveal.js/) framework.
- See index.html for the slide show composition
- See markdown/* for the slide sources.

### Run the slides from sources
- You can also run the slides locally in a container or from sources.
```
docker run -d -p 8080:80 --name slides \
  -v ${PWD}:/usr/share/nginx/html nginx
```
- Or use yarn
```
yarn && yarn start
```
- Open a browser [slides](http://localhost:8080/)
