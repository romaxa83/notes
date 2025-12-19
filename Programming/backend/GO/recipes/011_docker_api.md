#go #recipes #docker

Скрипт ниже реализует команды  `docker ps` и `docker image ls`

```go
package main

import (
	"fmt"
	"github.com/docker/docker/api/types"
	"github.com/docker/docker/client"
	"golang.org/x/net/context"
)

func listContainers() error {
	cli, err := client.NewEnvClient()
	if err != nil {
		return (err)
	}

	containers, err := cli.ContainerList(context.Background(),
		types.ContainerListOptions{})
	if err != nil {
		return (err)
	}

	for _, container := range containers {
		fmt.Println("Images:", container.Image, "with ID:", container.ID)
	}
	return nil
}

func listImages() error {
	cli, err := client.NewEnvClient()
	if err != nil {
		return (err)
	}

	images, err := cli.ImageList(context.Background(), types.ImageListOptions{})
	if err != nil {
		return (err)
	}

	for _, image := range images {
		fmt.Printf("Images %s with size %d\n", image.RepoTags, image.Size)
	}
	return nil
}

func main() {
	fmt.Println("The available images are:")
	err := listImages()
	if err != nil {
		fmt.Println(err)
	}

	fmt.Println("The running Containers are:")
	err = listContainers()
	if err != nil {
		fmt.Println(err)
	}
}
```

```go
// Определение структуры types.Container 
// ( https://godoc.org/github.com/docker/docker/api/types#Container ), 
// возвращаемой функцией ContainerList() , выглядит так:
type Container struct {
	ID         string `json:"Id"`
	Names	   []string
	Image      string
	ImageID    string
	Command    string
	Created    int64
	Ports      []Port
	SizeRw     int64 `json:",omitempty"`
	SizeRootFs int64 `json:",omitempty"`
	Labels     map[string]string
	State      string
	Status     string
	HostConfig struct {
		NetworkMode string `json:",omitempty"`
	}
	NetworkSettings *SummaryNetworkSettings
	Mounts          []MountPoint
}

// Определение структуры types.ImageSummary 
// ( https://godoc.org/github.com/docker/docker/api/types#ImageSummary ),
// которая является срезом, возвращаемым ImageList(), имеет следующий вид:
type ImageSummary struct {
	Containers  int64 `json:"Containers"`
	Created     int64 `json:"Created"`
	ID          string `json:"Id"`
	Labels      map[string]string `json:"Labels"`
	ParentID    string `json:"ParentId"`
	RepoDigests []string `json:"RepoDigests"`
	RepoTags    []string `json:"RepoTags"`
	SharedSize  int64 `json:"SharedSize"`
	Size        int64 `json:"Size"`
	VirtualSize int64 `json:"VirtualSize"`
}
```
---