# Diagnosing improper JavaFX thread access

With JavaFX many operations are expected to be done on the application thread. This isn't strictly enforced by the library in most cases, but not doing so can lead to non-deterministic behaviors or even exceptions. Finding out where these occur just by looking at code isn't easy when projects get beyond a certain size, so to facilitate this we created [JavaFX Access Agent](https://github.com/Col-E/javafx-access-agent). Its a Java agent that you add to your VM options when running Recaf that logs where improper thread access occurs. The agent is configured in Recaf's [`Main.java`](https://github.com/Col-E/Recaf/blob/master/recaf-ui/src/main/java/software/coley/recaf/Main.java) to print to std-err.

## Getting the agent

The agent can be downloaded on the GitHub project's [releases](https://github.com/Col-E/javafx-access-agent/releases) page or on [Maven Central](https://repo1.maven.org/maven2/software/coley/javafx-access-agent/).

## Setup

In IntelliJ you can create a run config _(See: [../arch/running.md](Running))_ with custom VM options specified. In the VM input field you will add  `-javaagent:<path>=<package-list>` where:

- `<path>` is the path to the agent jar.
- `<package-list>` is a semicolon separated list of internal package names. For instance: `software/;org/;com/;javafx/`