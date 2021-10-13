---
layout: post
title: "Dockerize F# with Paket"
categories:
  - Docker
  - .NET
  - F#
lang: en
---

I recently started learning [F#](https://fsharp.org/), the functional language for the .NET Framework.
So far, I enjoy it very much!  
To gain some experience using the language, I developed [RailMail](https://github.com/Skn0tt/RailMail).

After finishing the first features, I wanted to setup a Dockerfile - only to find out that it is not exactly trivial and there are no examples or tutorial that use [Paket](https://fsprojects.github.io/Paket/).

I wanted to make this tutorial so that you will have an easier time dockerizing your F# solution.

<!--more-->

## Dockerfile

There are two build stages involved:

1. Compile the sources
2. Configure the runtime

These steps will be explained, you can see an example `Dockerfile` at the end of the article.

### 1. Compile the Sources

*Paket* needs *Mono* to work, so the standard [microsoft/dotnet:sdk](https://hub.docker.com/r/microsoft/dotnet) image doesn't work for building.
Instead, I used [dcurylo/fsharp-mono-netcore](https://hub.docker.com/r/dcurylo/fsharp-mono-netcore), which extends that by `mono`.  
Notice the `AS`-Directive: This tags this build stage for latter reuse.

```dockerfile
FROM dcurylo/fsharp-mono-netcore AS build-env
WORKDIR /app
```

The first step is to install a new version of *Paket* using the bootstrapper.

```dockerfile
COPY .paket/paket.bootstrapper.exe .paket/
RUN mono .paket/paket.bootstrapper.exe
```

Then, the dependencies are restored.

```dockerfile
COPY paket.lock paket.dependencies ./
COPY .paket/Paket.Restore.targets .paket/

RUN mono .paket/paket.exe install
```

You then copy the solution sources and compile the app.

```dockerfile
COPY src/RailMail/** src/RailMail/
RUN dotnet publish -c Release -o out src/RailMail
```

### 2. Configure the Runtime

For the runtime, the standard [microsoft/dotnet:aspnetcore-runtime](https://hub.docker.com/r/microsoft/dotnet) image can be used.

```dockerfile
FROM microsoft/dotnet:aspnetcore-runtime
WORKDIR /app
```

Then, the build artifacts are copied from the last build stage (notice `--from=build-env`).

```dockerfile
COPY --from=build-env /app/src/RailMail/out .
```

The last step is exposing the standard ports and configuring the entrypoint.

```dockerfile
EXPOSE 80

ENTRYPOINT ["dotnet", "RailMail.dll"]
```

I hope this will help you when configuring your own Docker build with F#.  
Also, feel free to [have a look at the RailMail Project!](https://github.com/Skn0tt/RailMail)

## TL;DR

The finished `Dockerfile` could look like this:

```dockerfile
FROM dcurylo/fsharp-mono-netcore AS build-env
WORKDIR /app

# Add Paket
COPY .paket/paket.bootstrapper.exe .paket/
RUN mono .paket/paket.bootstrapper.exe

# Install Dependencies
COPY paket.lock paket.dependencies ./
COPY .paket/Paket.Restore.targets .paket/

RUN mono .paket/paket.exe install

# Build
COPY src/RailMail/** src/RailMail/
RUN dotnet publish -c Release -o out src/RailMail

# Copy to Runtime
FROM microsoft/dotnet:aspnetcore-runtime
WORKDIR /app
COPY --from=build-env /app/src/RailMail/out .

EXPOSE 80

ENTRYPOINT ["dotnet", "RailMail.dll"]
```
