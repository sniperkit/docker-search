# README #

docker-search: a better way to search the docker registry.

### Why? 

This fails right now

    $ docker search libavcodec
    NAME      DESCRIPTION   STARS     OFFICIAL   AUTOMATED

But, this works:

    $ docker-search -filter=libavcodec ffmpeg
    Name                          Description                   
    ----                          -----------                   
    bfirsh/ffmpeg 


### Usage


    docker-search: A better way to search the docker registry
    
    
    docker-search does a search against the Docker registry, and then pulls the Dockerfile and 
    searches inside it.

    Examples:
    
    docker-search -filter=ruby1.9 quantal coreos # Search for images with ffpmeg or coreos with ruby 1.9
    docker-search -filter=quantal ffmpeg  # Search for the string quantal in the Dockerfile
    docker-search -dockerfile ffmpeg # print out full dockerfiles
    docker-search -dockerfile -format=json ffmpeg # print out full dockerfiles as JSON
    
    Flags:
    
      -annotate=true: Annotation with Dockerfile information (faster without but no second level search)
      -dockerfile=false: Print out dockerfiles
      -filter=: List of filters
      -format="table": Format the output: table or json
      -generate-config=false: Generate a new default configuration file
      -verbose=false: Output verbose messages (false)


### Developer Details ###

docker-search works in three stages right now. 

* search: query the docker index
* query: annotate the search result with scraped data, like Dockerfiles themselves
* filter: filter through the data based on interesting information

This structure allows us to write tests since we can seed data (using the `Load` method 
on the client) that has us skip the first two stages. As long as our results are normal.

### The Configuration File ###

Why is there a configuration file? Well, the docker registry/index/hub search is a little confusing right now. 
The API documents indicated I should be able to use hub.docker.com to search, but then all these requests are either
301 or 302. Something is wrong. So, I sniffed the traffic from `docker search` to see what they were using (with this command `tcpdump -s 0 -A 'tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'`). They were using a strange IP and port, and I got it working with the following cURL command `curl -i 192.168.59.103:2375/v1.12/images/search?term=ffmpeg`. I figured this would change soon, and did not want to hard code the URL into the app. So, docker-search uses a configuration file which you can edit if the backend changes or solidifies.
### Contribution guidelines ###

