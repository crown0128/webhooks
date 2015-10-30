Library webhooks
================

[![GoDoc](https://godoc.org/github.com/joeybloggs/webhooks?status.svg)](https://godoc.org/github.com/joeybloggs/webhooks)

Library webhooks allows for easy recieving and parsing of GitHub Webhook Events; more services to come i.e. BitBucket...

Features:

* Parses the entire payload, not just a few fields.
* Fields + Schema directly lines up with webhook posted json

Notes:

* Github - Currently only accepting json payloads.

Installation
------------

Use go get.

	go get github.com/joeybloggs/webhooks

or to update

	go get -u github.com/joeybloggs/webhooks

Then import the validator package into your own code.

	import "github.com/joeybloggs/webhooks"

Usage and documentation
------

Please see http://godoc.org/github.com/joeybloggs/webhooks for detailed usage docs.

##### Examples:

Multiple Handlers for each event you subscribe to
```go
package main

import (
	"fmt"
	"strconv"

	"github.com/joeybloggs/webhooks"
	"github.com/joeybloggs/webhooks/github"
)

const (
	path = "/webhooks"
	port = 80
)

func main() {
	hook := github.New(&github.Config{Secret: "MyGitHubSuperSecretSecrect...?"})
	hook.RegisterEvents(HandleRelease, github.ReleaseEvent)
	hook.RegisterEvents(HandlePullRequest, github.PullRequestEvent)

	err := webhooks.Run(hook, ":"+strconv.Itoa(port), path)
	if err != nil {
		fmt.Println(err)
	}
}

// HandleRelease handles GitHub release events
func HandleRelease(payload interface{}) {

	fmt.Println("Handling Release")

	pl := payload.(github.ReleasePayload)

	// only want to compile on full releases
	if pl.Release.Draft || pl.Release.Prelelease || pl.Release.TargetCommitish != "master" {
		return
	}

	// Do whatever you want from here...
	fmt.Printf("%+v", pl)
}

// HandlePullRequest handles GitHub pull_request events
func HandlePullRequest(payload interface{}) {

	fmt.Println("Handling Pull Request")

	pl := payload.(github.PullRequestPayload)

	// Do whatever you want from here...
	fmt.Printf("%+v", pl)
}
```

Single receiver for events you subscribe to
```go
package main

import (
	"fmt"
	"strconv"

	"github.com/joeybloggs/webhooks"
	"github.com/joeybloggs/webhooks/github"
)

const (
	path = "/webhooks"
	port = 80
)

func main() {
	hook := github.New(&github.Config{Secret: "MyGitHubSuperSecretSecrect...?"})
	hook.RegisterEvents(HandleMultiple, github.ReleaseEvent, github.PullRequestEvent) // Add as many as you want

	err := webhooks.Run(hook, ":"+strconv.Itoa(port), path)
	if err != nil {
		fmt.Println(err)
	}
}

// HandleMultiple handles multiple GitHub events
func HandleMultiple(payload interface{}) {

	fmt.Println("Handling Payload..")

	switch payload.(type) {

	case github.ReleasePayload:
		release := payload.(github.ReleasePayload)
		// Do whatever you want from here...
		fmt.Printf("%+v", release)

	case github.PullRequestPayload:
		pullRequest := payload.(github.PullRequestPayload)
		// Do whatever you want from here...
		fmt.Printf("%+v", pullRequest)
	}
}
```

Contributing
------

Pull requests for other service like BitBucket are welcome!

There will always be a development branch for each version i.e. `v1-development`. In order to contribute, 
please make your pull requests against those branches.

If the changes being proposed or requested are breaking changes, please create an issue, for discussion
or create a pull request against the highest development branch for example this package has a
v1 and v1-development branch however, there will also be a v2-development branch even though v2 doesn't exist yet.

License
------
Distributed under MIT License, please see license file in code for more details.