## Multiverse OS Development Task List
###############################################################################
The order of importance/priority will be in levels, so that each item on a given
level is at the same priority level as everything else on the same level.
Meaning that developers interested in participating are encouraged to select
available items from the lowest levels but any item they feel comfortable doing
from that level. Once a level is complete, we all move to the next level.

However, it is worth noting, these are guidelines, and they are flexible. If a
developer is particularly interested in a task from a high level, unless it has
dependencies from a previous level, are welcome to take on any task they feel
capable of completing.

## Compiling Developer Tasks and Roadmaps
*This list is under development, this task list is an effort to merge task
lists being maintained by different developers outlining their own development
tasks.* It is actively being compiled as we merge all active developers task
lists in an effort to centralize developer tasks, reorganize priority, and to 
better synchronize development overall. 

Because of this, some tasks may be completed already and we are actively working
to merge in everything, organize the priority.

Eventually priority of tasks into a tree or mycelia based on task dependencies,
so that all tasks dependent on a specific task are branches from a specific root
task. This will make it very clear, almost like a skill-tree in a video game
what tasks are very important by determining which tasks are holding up large
amounts of other tasks. In addition, clearly show what skills are required to
complete a given task, so that new or active developers can very quickly see
where their volunteer time can be used most effectively, allowing developers
to spend more time developing and contributing instead of spending sometimes 
hours determining the best place to start or contribute.

To accomplish this task, all of our active developers are combining their task
lists and compile feature road maps to milestone goals such as alpha release, 
beta release, first release candidate. 

As the data is being compiled, it will be done so in JSON or YAML format so that
it can be pulled into the new [url=https://multiverse-os.org](Multiverse OS website)
which will be the meeting point, where discussion, research, and facilitate 
community polls and developer voting.

## Active Task List
*If you have not already, please contact the active development team and
introduce yourself before attempting to complete anything here. So that we can
better organize our efforts and ensure all volunteer time is maximally utilized
and no ones time is wasted unnecessarily.*



* Begin converting `provision.sh` shell scripts into Go software that reads
  configuration, such as packages to be installed and uninstalled, services
  to add start at boot, configuration files to install, with variables to
  insert into configuration. 
    1) Start with universe router and galaxy router provision scripts. I
       recently rewrote the universe router provision script and so it 
       best reflects how the Go version will function to merge all the
       provisoning scripts seamlessly together and move the customization
       for each router into a YAML configuration file 
    2) Next move to the host machine, and start by breaking up the the 
       sections of the script into functions. So for example, a section
       that adds "Allow {Bridge_name}" which for now reads "llow virbr0"
       which is currently "cat 'allow virbr0' >> /etc/qemu/bridge.conf"
       can be replaced with:

```
// Returns bool for success/fail, and err if failed
func AddQemuBridgePermission(bridgeName string) (bool, err) {
	// Step one ensure the bridge being passed exists, and has a valid name that is like smaller than 32 chars (actual validation to be determined)
	// We scan the file to see if it is already present so that we are not adding duplicate lines to the file
	// Then if if its not present, we use append to append the file and return true, nil	
}
```
      And so each part of the host provisoning script can be broken up
      like this into funcitons that can then be called in order of the
      original script, and as each one is completed it can be shown
      in the UI as:

```
  ...
  [Step 14] Adding bridges for userspace access to '/etc/qemu/bridge.conf' ...    [OK]
  ...
```
      Or the alternative failure could look like:
```
  ...
  [Step 14] Adding bridges for userspace access to '/etc/qemu/bridge.conf' ...  [FAIL]
  ...
  [Errors]
    [Step 14][error: Failed to append to 'bridge.conf' file, incorrect permissions or ownership: bridge.conf 0444 root:root]
    ...and other errors...
```

      Obviously an error this simple would simply be automatically resolved
      by the installer, but the idea is to present how the UI could be 
      pieced together once we have the individual functions that would 
      make up the install process. This would also allow us to piece them
      together in a complete `SetupBridge(bridgeName string, subnet string)`
      that could be accessible by a command-line tool to access accumulative
      portions of the installer that would make sense as command-line tools.
      And unlike `libvirt` or just creating a bridge with `brigectl` these
      would do all the associated setup so that these bridges would be
      accessible to non-priviledged or userspace Virtual Machines. 

* Build Multiervse HOST agent/daemon and systemctl (and eventually init.d)
  script to start at boot.
    1) To begin with, start with converting the `/etc/rc.local` functions so
       that we can stop using rc.local since it is deprecated and considered
       bad practice. 
    2) Next starting the virtual machines (universe router, galaxy router,
       and controller). Ideally, we want to watch the boot process of the
       virtual machine using a VSOCK or similar connection so that each
       machine only boots if the previous machine was successfully started 
       with a successful internet connection. This will allow us to stop
       the process and drop the user into a rescue mode after some automated
       attempts at fixing the issue like rebooting


