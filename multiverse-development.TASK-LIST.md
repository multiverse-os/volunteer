**This is an older task list with relevant items and completed items that needs to be merged into the new active task list for conversion to JSON/YAML for eventual source data for the "skill-tree"-type community tasks system.**
===============================================================================

# todo

* finsih setting up router controller

   * security.conf limits file
   * qemu bridge file to allow things (set permissions on qemu-bridge-helper)
   * 

# Multiverse OS Development/Management
The following is the list of the tasks that need to be done immediately, ideally this will be moved to a shared platform that volunteers can utilize to maximize their ability to contribute and avoid wasting time. 

Moving this into a web based visualization of this task list in a tree/mycielial data structure to indicate what tasks will enable other tasks to be accomplishable, indicating which tasks will be the most effective at pushing forwad the project as well as what tasks need to be done by specialists.


### The QEMU x86 emulation supports NVMe disk interface
We should ensure our access to this disk is not impeded by limitatations in p9, in fact we should stop using p9 altogether for linking filesystem with the VMs.

### Getting rid of `virt-manager` development dependency
**We need to stop using virt-manager IMMEDIATELY** it is very bad, it does not give an accurate representation of what options are avaialble, it does not provide adequate access to libvirt feautes, and very important, many of the options are completely depcreated, such as parallel connection. 

### Multiple bases are currently a development dependency
Ideally as `portal-gun` develops we will be able to share a single debian9 base, one for each (OS), or at the very least a base for each (OS+Multiverse VM Class). But for now since we can not eaisly provision the difference on each boot, we need to use unique bases that have their configuration _hard-provivisioned_ (think of hard-coding). The sooner we get `portal-gun` to this stage we can drop a lot of third-party dependencies and are essentially 90% ready for Alpha release.

> The `debian9.base` is sourced from the `debian9.user.controller.base`, we need to rebuild a fresh install for an official `debian9.base`.



   [*][Convert all **FORMULA-IMAGES** to Qcow2] this will allow them to
    | snapshot and they do not require fast speeds since they are only
    | used at install and update. 
    |
    [->][Specifically convert raw the remaining *.raw formula image] 
    | `/var/multiverse/images/formula-images/`+
    | `portal.controller.user.formula.raw`;
    [->][portal.controller.user.formula.raw] should be `qcow2`, for the
    | snapshotting ability, layers, compression and other useful features
    | since the speed downside does not matter since its only used at
    | install and update. 
    |
    [->][something]
    details


   [*][Convert all routers to use new VM structure]
    |
    [->]
    
  

   [*][Complete the encapsulation of routers on the HOST]
    |  Determine the best way to utilize the **ROUTER CONTROLLER VM**
    |  concept. 
    |
    [->]


   [*][Rebuild all the app/service VMs] utilizing the new 3 image
    |  segregated design 
    |
    [->]
 

   [*][Build a daemonizer kernel module, that runs signed code] since it
    |  is difficult to make a non-C kernel module, we will have C embed 
    |  the Go binary and execute the code in the kernel module, probably 
    |  in a container or some other isolated jail. 
    |
    |  The kernel module will feed packets to the web services direclty
    |  bypassing other layers. It will be able to create devices. Ensure 
    |  the process stays open, and disable all other operaitons on the VM 
    |  (no in/out without signed/ecypted data).
    |
    [->]

   [*][Figure out the best way to get console without virt-manager]
      In `35.8 of the Red Had Libvirt Docs` fully virtualized guest
      operating system guest console access is done by:

	|> "Modifying the `grub.conf` file and include the kernel
	|> line and append: `console =ttyS0 console=tty0`

      Additionally, a complete review of our options is found in `20.23`
	
    If we could use `raw sockets` that would be ideal, raw sockets do not
    care about protocol headers and such and allow for creation of unique
    protocols to transfer datagrams (as opposed to say TCP/UDP protocols). 
    This allows for the creation of unique protocols even in places like
    userspace.

    We want to avoid `datagram sockets` since these type have the address
    on each packet, so they are independent, but we dont need that data
    since the connection we are creating is `1-to-1` or `N-to-1`.

    And `stream sockets` are typically built ontop of TCP, and have a lot of
    logic to ensure ordering, which is not really necessary, and just slow
    down the transfer of our data. IF anything we want something with as
    little logic as possible ontop of the transfer of data. Possibly just
    virtual differential cables, or similar.

    By sticking with the packet model, we can easily scale to multiple
    machines using standard network, in additon we can drop in our single
    binary routers to control the flow of packets. 

	[!][Resources]
	[Virtual_circuit](https://en.wikipedia.org/wiki/virtual_circuit)
	[20.16.16 Channel Red Hat Docs]
	[20.16.17 Host physical machine interface]
	[23.18 Devices (VirtIO]
          virtio-sci-pci, virtio-blk-pci, virtio-net-pci, virtio-serial-pci
          virtio-balloon-pci, virtio-rng-pci

    **Character Device (or 'chardev')**
      Each chardev element has an optional sub-element `address` that can
      connect the specific chardev to a particular:

	<device>
	  <serial>
	    <source> this indicates the host machine location, often the
	             file system, such as `/dev/somthing`

	    <target> this element controls the bus or device the disk is
		     exposed to the guest machine operating system. 
		     target has an optional `bus` attribute with optional 
		     values such as: [ide, scsi, virtio, kvm, usb, sata]
		     default is inferred from device name, such as sda
		     indicates `sata`. the attribute `tray`, as in cd-rom
	             tray, or floppy, meaning it implies removable media.
	             the two (2) values `tray` can have is [open, closed]

	    <iotune> enables allows the control of read or write speed

	    <driver> allows specifying hypervisor (HOST) driver that is
          	     used to provide the disk. the attributes include `name`
  	             as well as `cache` which should be set to `none`. 
		     as well as `error_policy attribute, that has possible
	             values of [stop, report, ignore, report] and lastly
         	     an `io` attribute, that controls specific policies 
		     and values of [native, threads, ioeventfd
           	     `event_idx` attribute controls aspects of event
		     processing can be set to [on, off]. on reduces the
	   	     number of interrupts and exits for the guest virtual
	  	     machine. just let it be default. `copy_on_read`
		     attribute controls whether to copy the read backing 
	             file into the image file. accepted values can be either
	  	     [on, off] avoids accesing the same backing file sectors
	             repeatedly and is useful when the backing file is over 			     slow network by default it is _off_. since we want to
	             share a single base file, **set this to on on shared**
		     **devices like base images** 
               	     
	    <boot>   this has sub-elements <order> which is obvious and
	             <per-device> cannot be used together with general boot 	 	  	     elements in BIOS boot loader section.
      <encryption>   this


	<readonly>   to set it as read-only, not modifiable by guest

       <shareable>   indicates the device is expected to shared across 			     domains. **we will be using this for base images**
	             **IF THIS IS USED, CAHCE MUST BE NONE** AND we should
		     `copy_on_read` should be used to increase speed and
	             efficency.

	<transient>  basically ephemeral, device contents should be revered
            	     when the guest machine shuts down. 

	   <serial>  specifies SERIAL NUMBER, not the other use of serial
             	     this is **important to use**. Example:
	               <serial>WD-WMAP9A</serial> 
              	     **We should come up with a way to generate realistic**
	             **serial numbers for our devices to hide the fact**
		     **that they are virtual**

	      <wwn>  description

	   <vendor>  description

	  <product>  description

	     <host>  description
		  
	             **Host sub-attributes**

                     <nbd>
                     <rbd>
                     <sheepdog>
                     <gluster>

 

	  </serial>
	</device>

      [controller]: as in [
      

	|> "A character device presents itself to the host physical machine
	|> as one of the following types:"

      [*][Domain Logfile] Disables all input on the chardev, and sends 
	 output to the virtual machines logfile. 

      [*][Device logfile] A file is opened and all data sent to the chardev
	 is written to the file

      [*][Virtual Console] Connects the chardev to the graphical framebuffer
	 in a virtual console. This is typically accessed via special hotkey
	 sequence such as `ctrl+alt+3`

      [*][Null Device] Connects the chardev to the void.

      [*][Pseudo TTY] A pseudo TTY is allocated using `/dev/ptmx`
	 Accessible via a client such as virsh console or interact with the
	 local serial port

      [*][NB Special Case] NB special case if <console type="pty"> then the 
	 `TTY` path is also duplicated as an attribute `tty='/dev/pts/3'` on
	 the top level <console> tag. This provides compatiblity with 
	 existing syntax for <console> tags.

      [*][Host physical machine device proxy] The character device is passed
	 through to the underlying physical character device. The device 
 	 types must match, e.g. *the emulated serial port should be*
	 *connected to a host physical machine serial port*
         (Like a passthrough?)


      [*][Named pipe] The character device writes output to a name pipe. See
	 See pipe(7) man page for more info. 

  	__NOTE__
	**This may prove to be the best option since unlike the above**
  	**Since unlike the above options, it does not have artificial**
     	**speed limitaitons to make it compatible with the existing**
    	**serial port protocls and devices.**

      [*][TCP client/server] While there is is essentially no packet loss
	 due to network connectivity or wire issues since the wires are
	 logical, the TCP abstraction adds unncessary checking, and logic
	 that will cause unnecssary latency.

      [*][UDP network console] Since UDP is just sending packets and
	 not checking anything unnessarily, which without any packet loss
	 is likely the most ideal way to send data. **And because of this**
	 **lack of unnecessary checking added for issues with physical**
	 **networking issues, UDP is a wise choice.**

      [*][Unix domain socket] **Another wise choice because these are**
	 **specfically designed for logical connections between processes**
	 **otherwise known as** _inter-process-communicaiton_ or __(IPC)__
	
	 This is a data communicaitons endpoint for exchanging data between
	 processes, executing on the same host, like **named pipes**, unix
	 domain sockets support transmission of a __reliable stream__ of
	 bytes. Because we know the communication is relaible since its 
	 on virtual lines, we should avoid TCP.

	 All communicaiton is similar the the internet protocol, as in it 
	 uses packets. But all communication happens within the kernel.

	 Unix domain sockets use the filesystem as their address namespace.

	 Not only data but file descriptors (FDs) can be sent over sockets.
	 This is done using the `sendmsg()` and `recvmsg()` syscalls. What
	 this means is that the sending processes can grant access to 
	 aspects of the system the receivng process could not normally 	 	  access. _Without needing to send the requests to do an action and_
	 _send back the respond of the action, its a more efficient style_
	 _of proxying. This allows unpriviledged applicaitons access to_
	 _aspects of the system they normally couldn't while keeping all_
	 _the logic for what the unpriviledged process needs to do in the_
	 _unpriveledged process._

	 Unix domain sockets have subclasses, or types, there are three that
	 can be compared to the three (3) standard internet datagrams.

	   (1)[**SOCK_STREAM**][TCP] Has extra logic on the protocl to 
	      enforce the reliable ordered stream of bytes

	   (2)[**SOCK_SEQPACKET**][SCTP] Like a hybrid between TCP/UDP, it 
	      both has additional logic on the protocl to enforce reliable
	      ordering of packets, and is "message-oreiented like UDP". 

	      The unique feature is **multi-homing** and reduntant paths to
	      increase resilience and relaibility. 

	      This extra logic for resilience and reliability is not
	      necessary unless we are talking to an remote Multiverse OS 
	      instance. But currently we are only dealing with local HOST
	      in a single bare-metal machine cluster. This may be more useful
	      in the future when we are clustering multiple bare metal
	      machines into a Multiverse OS super cluster.

	   (3)[**SOCK_SOCKDGRAM**][UDP] UDP lacks the extra logic on the
	      protocol for ensuring reliable transmission and ordering of
 	      packets. Since we are moving through the same machine on
	      virtual connections, this is the best option.

      [*][

      [*][
 
	<serial type='pty'>
	  <source path=/dev/pts/3>
	  <target port=0>

	<channel type=unix>
	  <source mode=bind  path=/tmp/guestfwd>
	  <target type=guestfwd address=x port=x>


   [*][Find ways to optimize the security of the VMs][Hardening Multiverse]
    |
    [->][Figure out best ways to avoid hypervisor control over VMs]
    |
    [->][Ensure VMs can not possibly over commit memory or CPU]
    |
    [->][Lockdown the HOST]
    |
    [->][Lockdown the CONTROLLER]
    |
    [->][Lockdown the routers]
    |
    [->][Patch the kernel to control stdin/stdout/err]
    |
    [->][Build lockdown kernel modules]
    |
    [->][Allow only signed kernel modules]
    |
    [->][Build custom initramfs]
    |
    [->][Build custom cryptsetup]
    |
    [->][Improve the LUKs backup process by]
    |   always saving the headers with the key]
    |
    [->][ELSE]
	  [kmod]
	  * `allow_unsafe_assinged_interrupts 0`

	  [smart]
	  * disable smart, it should only be used on physical NOT 
	    virtual HDs

  	    `chkconfig --del smartd`


   [*][PCIe virtual network cards to do VM-to-VM] communication and
    | networking. 
    |
    [->][Determine the best way to handle the communication] It needs to
    |   satisfy the following requirements: 
    |   (1)[Not capable of being tampered with by the a malicious HOST]
    |    |-
    |    |
    |    |-
    |    |
    |    | 
    |   (2)[Not be limited by artificail speed limitations] like aritfical
    |    |  speeds to match serial speed needed to emulate physical devices.
    |    |  We are virtual, making our speed limited artifically to match a
    |    |  protocl we are not even interfacing with is "flaw".
    |    | 
    |    | Review the Red HAT device and VirtIO decumentation, its the most 
    |    | reliable source of information about libvirt even more than the
    |    | official libvirt docs. 
    |    |
    |    |-[>] `HOST physical machine device proxy` There is even 
    |    | experimental kernel based routing, which could be used
    |    | deeper in the cluster since breakouts lead to other VMs possibly
    |    | since the kernel is being shared is a virtual kernel (but its 
    |    | also KVM relying on whcih is from the HOST kernel, so be
    |    | careful). 
    |    |
    |    |-[>] `VirtIO NAMED PIPE`
    |    |
    |    |-[>] `VirtIO based UDP Network Console` which for video streaming
    |    |     is far better than the TCP options commonly used like x over
    |    |     SSH, or even xpra which uses TCP. UDP can be improved a lot 
    |    |     to: 
    |    |
    |    |     **(check out** [__kcp library for go__] **which does both**
    |    |     **multiplex implement some of the TCP features ontop of the**
    |    |     **UDP like video game multiplayer code used in FPS. And even**
    |    |     **uses the algorithm (PUT THE NAME IN HERE I FORGOT IT) that**
    |    |     **is often used in wifi to do correction in a very efficient**
    |    |     **way.)**
    |    |
    |    |
    |   (3)[NIC Passthrough] Two (2) ways of doing passthrough with libvirt:
    |    | 
    |    |-[>] conventional method is with the <hostdev> element straight
    |    |     forward
    |    |
    |    |-[>] new method is using <interface type="hostdev"> element
    |    |
    |    |
    |   (4)[]
    |    |
    |    |-
    |    |
    |    o
    |
    [->]




  [*][Attach our single binary ROUTERs to each VM] To provide complex
   | networking direclty above and esnure isolation.
   |
   [->]
   
  [*][Make Daemon for host to laucnh VMs]
   |
   [->]


  [*][have router VMs report boot status and internet status] for automated
   | booting that does not fail
   |
   [->]


  [*][build database for all images]
   |
   [->]


  [*][build early version of scramble suite identity daemon and CLI]
   |
   [->]

  [*][Build a multiverse OS development blog and begin trying to make it]
   | to track what has been done, the history of the project, topics that 
   | currently being researched so other developers and researchers can 
   | utilize any of the research already done in developing Multiverse OS. 
   |
   | Additionally, make it easier for new developres to review the project 
   | and quickly determine if there are any tasks that they can do to 
   | contribute to the project.
   |
   [->]


  [*][Memory backing needs to be optimized further] In addition to ensuring
   | there is no possible way to overcommit memory on both the HOST level
   | or the USER CONTROLLER level we need to do a lot to further optimize
   | the _memory backing_
   |
   | Belos is just a very rough draft of some of the options that can be
   | set to optimize the memory backing much further:
   |
   |	<memorybacking>
   |	<hugepages>
   |	  <page size=1" unit"=g" nodesetx
   |
   |	<nosharedpages/>
   |
   |
   [->] **Huge Pages** configuration will make memory intensive servers much
   |    noticeably more fast along with helping avoding overlapping memory
   |    issues from balooning type issues.
   |
   [->] **nosharedpages** needs to be configured and beyond that ensure that
   |    all memory is encrypted to the VM and not accessible to any other 
   |    device, not even the hypervisor without the key held WITHIN the VM. 
   |
   [->] **Reserach NUMA** to determine if this will be necessary


  [*][Build script/software to determine best clock configuration]
     Eventually it would be great to design a PCI device with several
     RTC clocks that can be passed to each VM so they have physical clocks
     which would solve A LOT of issues.


  [*][Experiment with booting direclty to signed custom init or BIOS]
     Figure out best way to incorporate signed uboot bios for maximum
     capbility across as many devices as possible.

  [*][Prototype our custom Multiverse OS keyboard in virtual space]
     By implementing a virtual keyboard that we can build with software
     over the default libvirt one. 

  
_____
## Current list of Portals (VMs) Formulas to be released on Alpha Release
Below is the evolving list of Multiverse OS portals (VMs) that are planned to be released with the Alpha version fo Multiverse.

````
	portalgun
	├── application
	│   ├── browser
	│   │   ├── chromium
	│   │   ├── firefox
	│   │   └── torbrowser
	│   ├── console
	│   ├── filebrowser
	│   ├── media
	│   ├── project
	│   │   ├── audio
	│   │   ├── development
	│   │   │   ├── hardware
	│   │   │   │   └── go
	│   │   │   └── software
	│   │   │       ├── c
	│   │   │       ├── cpp
	│   │   │       ├── ruby
	│   │   │       └── rust
	│   │   └── visual
	│   └── steam
	├── controller
	│   ├── router
	│   │   └── formula
	│   └── user
	│       └── formula
	└── service
	    ├── client
	    │   └── torrent
	    ├── crypto
	    │   └── wallet
	    │       ├── bitcoin
	    │       └── ethereum-classic
	    ├── router
	    │   ├── galaxy
	    │   ├── star
	    │   └── universe
	    └── server
		└── web

````
_____
## NOW
  * Build alpine base image for rebuild routers. 
  * Make router formula images
  * use virtio output file to indicate time-till(TT)-boot in ms. Then TT-internet and if the internet is required to reconnect. display WAN deets (ip) lan deets (ip). and a list of processes and details. all in CBOR or some other binary based format readable by computers. this until we can hook it directly to the controller.
  * Build a DB system to manage all the VM images, and even the scramble-id images. Make a qcow2 client that is capable of combining and disassembling qcow2 files into heriarchal trees using merkle trees (then transferalbe by torrent in individual chunks. so you can request just 1 sub identity on the fly from a remote machine.

  * VM-to-VM interface: special custom virtio pcie cards that enable connections between each other directly over /var/multiverse/interface on host 

  * take our single binary alpine see if we can insert in a bios or some other way to generate an environment that is deterministic and ecnrypted. 
  
  * build out scramble-id image (maybe raw or qcow2), inside store qcow2 of home folder for controller. but then also be able to generate on the fly home folders for specific sub-ids.


  * Build a kernel module that will provide agent support in all Multiverse OS machines. it will launch the process and keep it alive or reboot. this will be the foundation for security features and true lockdown of our various systems. (to avoid tainted messages, you have to include open source license in code)


  * start portal gun with libvirt-go[+ libvirt-go-xml] (or libvirt-rust)
    [look at libvirt sandbox for individual binary creating in c]
    + incorporate libosinfo - operating system info db 


  * start docs and making multiverse md, use chroma for blog,

================================================================
## EVENTUALLY
  * Build an example formula with a manifest file of some sort
    dictating preferably the ruby script (maybe include ruby, python
    crystal or other binaries in the build VM to give options)
    
  * A build VM that is built in a determinsitc trustless way
    to make determinstici trustless builds

  * experiment with building our own console using wayland+vulkan. It will interact with the linux input and output, (support multiverse os signed and encrytped input/output evnetually), print text crisp in 4k, support updatable selctions of text, ruby as a scripting instead of bash, -- you would start with a just CLI verison probably, then build the UI for it. have a sever so it supports coop mode out of the box, with multiple cursors (maybe you can take over, merge or split cursors). 

  * scmralbe id system server supports DNS like system, combine or merge qcow2 images or generate virtaul images exposing home var and tmp on the fly.

  * scrmable identity key will hold key/value databases to hold data crawled from net, etc
    * libguestfs = agent for modifying images of VM (like on-the-fly changing of HD size) 
_____
## Template to add items to the above list

  [*][A brief title to describe the task]
   | An in depth description of the task
   |
   [->] A sub task ________________________________________________________
   |    and all the details 
   |    (1)[Not capable of being tampered with by the a malicious HOST]
   |     |-
   |     |
   |     |-













