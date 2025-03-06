202212151441
Status: #idea
Tags: #work #docker #programming 

# project_ramona_notes

Run the [[docker]] container:
```bash
docker_run.sh project_ramona:autonomy-rc
```

Run the simulator:
```bash
/opt/greensea/ramona/bin/start_simulator.sh
```

Open workspace:
```bash
workspace --cfg_file=/home/nanderson/projects/ramona/so_config.yml
```

To open a new terminal session in a running container. This is required to run something else alongside workspace:
```bash
docker exec -it [container id] bash
```

Note on saving changes. If you modify the container that you're working in, you have to run:
```bash
docker commit [container id] [image name]
```
If `[image name]` is not included a new, nameless image will be created. Images can be overwritten, or created again with a new tag.


## Missions

Config file location
```bash
--config_file /opt/greensea/ramona/config_files/gss_lonely_diver_autonomy/config.yml
```

### Working With It
blackboard is in the libopensea_behavior_trees

libramon_tasks - why not in the project?
missions vs missions

### Talking to Jainne

disable all autos - libgss_tasks
derive from leaf node

destructors - have to unsubscribe or it will break. don't need to subscribe if we're just reading from BB or accepting yml param
need at min:
config
write_config
tick
cancel
reset

state machine 'ticks' until we set success or failure
configure() will bind the yml params to a member attr of the class/node
params are OBT::Parameter, have to access the value with .Value()


write config maybe passes stuff onto the next node/tree

use params to make leaf nodes toggleable
	
conditional looks to the blackboard

how do I get things to and from the blackboard?

does operator request write things to the blackboard? has it been written?

<command> = continue? Is it an if node?


### Validate user abort acceptance

- All available params produce the desired configurability
- The node can handle validation for any initial abort state
- Node can receive LCM when ticking
- Node can receive LCM when not ticking
- Node publishes expected digital values

    ABORT_TYPE_No_abort      = 0, ///< No abort commanded
    ABORT_TYPE_Exfil_abort   = 1, ///< Exfiltrate vehicle
    ABORT_TYPE_Go_home       = 2, ///< Go to operator defined location
    ABORT_TYPE_Surface_abort = 3, ///< Rise to surface
    ABORT_TYPE_Neutral_abort = 4  ///< Abort in place, no additional command

# References

