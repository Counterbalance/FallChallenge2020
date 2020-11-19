# FallChallenge2020
Source code for CodinGame's Fall Challenge 2020 event.

# CLI and Brutaltester

This version contains a [brutaltester](https://github.com/dreignier/cg-brutaltester) compatible commandline interface.

Build with `mvn package` (or `mvn install`).
This will generate an executable jar with all dependencies bundled in in `target/fall-2020-1.0-SNAPSHOT-shaded.jar`.

Run with `java -jar fall-2020-1.0-SNAPSHOT-shaded.jar` to see usage.

## Example of a local run
This starts a server on http://localhost:8888 after running:
```
java -jar fall-2020-1.0-SNAPSHOT-shaded.jar -league 3 -p1 main.exe -p2 old.exe -l ./logs/run.json -s
```
You can specify a seed with the `-seed 123` argument, or alternatively, with `-d seed=123` (which is what brutaltester does).

You can specify a commandline for each bot by quoting the argument. For instance, to run a python bot, specify `-p1 "python bot.py"`,
or to pass arguments to a native bot specify `-p1 "main.exe some arguments"`.

## Example of a brutaltester run:

This will run a 100 games using 1 threads in brutaltester and store all the logs in ./logs:
```
java -jar cg-brutaltester-0.0.3-alpha-4-SNAPSHOT.jar \
  -r "java -jar fall-2020-1.0-SNAPSHOT-shaded.jar -league 3 -timeout 300" \
  -p1 main.exe \
  -p2 old.exe \
  -t 1 \
  -n 100 \
  -v -l ./logs/ 
```

## Timeouts and patching the CG engine

When brutaltesting it can often happen that your bot will time out because of operating system load. This happens frequently if the number of threads passed with the `-t` option equals the number of CPU hardware threads.

The default timeout of 50ms can be increased with by passing either `-d timeout=...` or `-timeout ...` to the referee.
The CG engine has hard limits on the total game duration (max 30000 ms or 30s). Since this game has a maximum of a 100 turns, we can set the timeout to 300 (ms) at most.

To increase the timeout further, the cg game engine needs to be patched. This involves the following steps:

1. Clone the CG game engine from https://github.com/CodinGame/codingame-game-engine.git
2. Apply the patch: `patch -p1 < engine-timeout.patch`. You can find this file next to this README.
3. Install the game engine with `mvn install`. This will install the 'master-SNAPSHOT' version of the engine to your local maven repository and make it available for other projects.
4. Update the `pom.xml` for this referee and change `<gamengine.version>3.15.4</gamengine.version>` on line 11 to say `<gamengine.version>master-SNAPSHOT</gamengine.version>`
5. Rebuild the referee with `mvn package`.

You can now specify any timeout you want. I recommend using `-timeout 2000` to deal with any spikes in the load.
