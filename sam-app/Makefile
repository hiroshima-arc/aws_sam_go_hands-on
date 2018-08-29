.PHONY: deps clean build

deps:
	go get -u github.com/aws/aws-lambda-go/...

clean: 
	rm -rf ./hello-world/hello-world
	
build:
	GOOS=linux GOARCH=amd64 go build -o hello-world/hello-world ./hello-world