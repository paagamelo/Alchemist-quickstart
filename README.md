# Alchemist quick start

This project is a quick start for the [Alchemist](https://github.com/AlchemistSimulator/Alchemist) simulator, it shows how to use the simulator via [Gradle](https://gradle.org) to run a simple simulation. More information can be found on [the official Alchemist website](https://alchemistsimulator.github.io).

## Prerequisites

Alchemist's prerequisites can be found [here](https://alchemistsimulator.github.io/wiki/usage/installation/).

## How to launch

To run the example you can rely on the pre-configured [Gradle](https://gradle.org) build script. It will automatically download all the required libraries, set up the environment, and execute the simulator via command line for you.
As first step, use `git` to locally clone this repository.
In order to launch, open a terminal and move to the project root folder, then on UNIX:
```bash
./gradlew runAlchemist
```
On Windows:
```
gradlew.bat runAlchemist
```

Press P and you will see some pedestrians wandering around. For further information about the gui, see the [graphical interface shortcuts](https://alchemistsimulator.github.io/wiki/usage/gui/).

Note that the first launch will be rather slow, since Gradle will download all the required files. They will get cached in the user's home folder (as per Gradle normal behavior).

## The build script

Let's explain how everything works by looking at the `build.gradle` script. First of all, we need to set Alchemist as a dependency, thus you will see something like this:
```kotlin
dependencies {
    implementation("it.unibo.alchemist:alchemist:SOME_ALCHEMIST_VERSION")
}
```
Nothing special actually. 

Now, let's look at the `runAlchemist` task, it is a simple gradle task responsible for launching the simulation. Let's dissect it:
```kotlin
tasks.register<JavaExec>("runAlchemist") {
    classpath = project.sourceSets.getByName("main").runtimeClasspath
    main = "it.unibo.alchemist.Alchemist"
    args = listOf("-y", "src/main/yaml/$simulation.yml")
}
```
[Gradle](https://gradle.org) has a special task to run a Java class from the build script: `JavaExec`. We can create our custom task of type `JavaExec` and configure it to launch our simulation. In order to make it work, we need to do two more things:
- specify the Alchemist main class, which is `it.unibo.alchemist.Alchemist`
- explicit the classpath, or java won't be able to find all the classes needed

This is what we do in the first two lines of code, and it is sufficient to successfully start Alchemist. Now, to make it run our simulation we need to launch the simulator with proper arguments. To run a simulation we can rely on the `-y` option followed by the path to the simulation file. For further information about the supported options see the [command line interface](#command-line-interface). Alchemist simulations are contained in *.yml files, more information about how to write such simulations can be found [here](https://alchemistsimulator.github.io/wiki/usage/yaml/). Let's suppose the `$simulation` variable contains the name of our simulation file, which is located in the `src/main/yaml/` folder, what we want to do is to run Alchemist with the following arguments:
```bash
-y src/main/yaml/$simulation.yml
```
The third line of code does exactly this. 

Ok, that's it. You should be able to use Alchemist via Gradle in your own project now, or at least have a clue.

## Command line interface

The CLI supports the following options

| Option                                     | Effect                                                                                                                                                                            |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -b,--batch                                 | Runs in batch mode. If one or more -var parameters are specified, multiple simulation runs will be executed in parallel with all the combinations of values.                      |
| -bmk,--benchmark <file>                    | Performs a benchmark with the provided simulation, measuring the total execution time. Saves result in given file.                                                                |
| -cc,--comment-char                         | Sets the char that will be used to mark a data file line as commented. Defaults to #. (To be implemented)                                                                         |
| -d,--distributed <file>                    | Distribute simulations in computer grid                                                                                                                                           |
| -e,--export <file>                         | Exports the results onto a file                                                                                                                                                   |
| -g,--effect-stack <file>                   | Loads an effect stack from file. Does nothing if in headless mode (because --batch and/or --headless are enabled)                                                                 |
| -h,--help                                  | Print this help and quits the program                                                                                                                                             |
| -hl,--headless                             | Disable the graphical interface (automatic in batch mode)                                                                                                                         |
| -i,--interval <interval>                   | Used when exporting data. Specifies how much simulated time units should pass between two samplings. Defaults to 1.                                                               |
| -p,--parallelism <arg>                     | Sets how many threads will be used in batch mode (default to the number of cores of your CPU).                                                                                    |
| -q,--quiet                                 | Quiet mode: print only error-level informations.                                                                                                                                  |
| -qq,--quiet-quiet                          | Super quiet mode: the simulator does not log anything. Go cry somewhere else if something goes wrong and you have no clue what.                                                   |
| -s,--serv <Ignite note configuration file> | Start Ignite cluster node on local machine                                                                                                                                        |
| -t,--end-time <Time>                       | The simulation will be concluded at the specified time. Defaults to infinity.                                                                                                     |
| -v,--verbose                               | Verbose mode: prints info-level informations. Slows the simulator down.                                                                                                           |
| -var,--variable <var1 var2 ... varN>       | Used with -b. If the specified variable exists in the Alchemist YAML file, it is added to the pool of  variables. Be wary: complexity quickly grows with the number of variables. |
| -vv,--vverbose                             | Very verbose mode: prints debug-level informations. Slows the simulator down. A lot.                                                                                              |
| -vvv,--vvverbose                           | Very very verbose mode: prints trace-level informations. Slows the simulator down. An awful lot.                                                                                  |
| -y,--yaml <file>                           | Load the specified Alchemist YAML file                                                                                                                                            |