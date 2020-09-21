# Systemd

## Disclaimer

* I can't seem to find every info in the documentation, so certains things aren't clear
* For example, inside some of my `.mount` files, this is written :-

```text
[Mount]
Type=admin
# Add a dummy argument after pkexec, or '/bin/sh -c' will eat the first argument in '$@'
Exec=/bin/sh -c 'pkexec /usr/libexec/gvfsd-admin "$@" --address $DBUS_SESSION_BUS_ADDRESS' gvfsd-admin
AutoMount=false
DBusName=org.gtk.vfs.mountpoint_admin
MountPerClient=true

```

* But no trace of `DBusName` & `MountPerClient`, even in`man systemd.mount`, `man systemd.exec, man systemd.kill` & `man systemd.unit`..

## Generalities

* System & service manager spawned after boot \(with a PID of 1\) and at each user login to starts what is needed
* Replace `init`, based on `upstart` \(for mac\)
  * In part thrown upon because it doesn't follows the Linux philosophy to do only one thing
  * However, it's still everywhere
  * In part compatible with init & SysV scripts
* Rarely used directly by users, prefer `systemctl` for that
* On boot, it reads `default.target` which is usually a symbolic link to `graphical.target` or `multi-user.target`

## Units

* There's 11 entities called **Unit**
* Some configs are  common to all of them, some are specific to one
* Process spawned by systemd are placed in cgroups of the name of the unit 

### Types

<table>
  <thead>
    <tr>
      <th style="text-align:left">Type</th>
      <th style="text-align:left">Extension</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Service</td>
      <td style="text-align:left"><code>.service</code>
      </td>
      <td style="text-align:left">Describe how to manage a service</td>
    </tr>
    <tr>
      <td style="text-align:left">Socket</td>
      <td style="text-align:left"><code>.socket</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Describes a network/IPC socket or a FIFO buffer that systemd uses for
            socket-based activation</li>
          <li>Always has an associated <code>.service</code> file that will be started
            when activity is seen on the socket</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Target</td>
      <td style="text-align:left"><code>.target</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Provides synchronization points for other units when booting up or changing
            states.</li>
          <li>Other units specify their relation to targets to become tied to the target&#x2019;s
            operations</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Device</td>
      <td style="text-align:left"><code>.device</code>
      </td>
      <td style="text-align:left">Describe a device that needs systemd (not all of them do)</td>
    </tr>
    <tr>
      <td style="text-align:left">Mount</td>
      <td style="text-align:left"><code>.mount</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Control mount points</li>
          <li>Named after the mount path (with - instead of /)</li>
          <li>Entries within <code>/etc/fstab</code> can have units created automatically</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Automount</td>
      <td style="text-align:left"><code>.automount</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Configures a mount point that will be automatically mounted</li>
          <li>These must be named after the mount point they refer to and must have
            a matching <code>.mount</code> unit to define the specifics of the mount</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Timer</td>
      <td style="text-align:left"><code>.timer</code>
      </td>
      <td style="text-align:left">Defines a timer, similar to a cron job</td>
    </tr>
    <tr>
      <td style="text-align:left">Swap</td>
      <td style="text-align:left"><code>.swap</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Describes swap space on the system</li>
          <li>The name of these units must reflect the device or file path of the space</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Path</td>
      <td style="text-align:left"><code>.path</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Defines a path that can be used for path-based activation</li>
          <li>By default, a <code>.service</code> unit of the same base name will be started
            when the path reaches the specified state</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Slice</td>
      <td style="text-align:left"><code>.slice</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Associated with Linux Control Group nodes, allowing resources to be restricted
            or assigned to any processes associated with the slice</li>
          <li>The name reflects its hierarchical position within the <code>cgroup</code> tree</li>
          <li>Units are placed in certain slices by default depending on their type</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Scope</td>
      <td style="text-align:left"><code>.scope</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Created automatically from information received from bus interfaces</li>
          <li>Used to manage sets of system processes that are created externally</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

### General Config

* Options are space separated list \(except for Description\)
* Format `Option=value_list`

#### \[UNIT\]

<table>
  <thead>
    <tr>
      <th style="text-align:left">Option</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Description</td>
      <td style="text-align:left">
        <ul>
          <li>Simple name description (not a sentence)</li>
          <li>Something like &quot;Apache2 Web Server&quot;</li>
          <li>Will be used in these kind of sentences &quot;Starting ...&quot;, &quot;Failed
            to start ...&quot;</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Documentation</td>
      <td style="text-align:left">
        <ul>
          <li>List of uri (ordered by relevance)</li>
          <li>Accepted uris are : &quot;http://&quot;, &quot;https://&quot;, &quot;file:&quot;,
            &quot;info:&quot;, &quot;man:&quot;</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Wants</td>
      <td style="text-align:left">Ask for target units, but can run even if they fail</td>
    </tr>
    <tr>
      <td style="text-align:left">Requires</td>
      <td style="text-align:left">Needs target units, will stop if they fails (but not for all cases where
        they stops)</td>
    </tr>
    <tr>
      <td style="text-align:left">BindsTo</td>
      <td style="text-align:left">Needs target units, will stop if they fails or stop</td>
    </tr>
    <tr>
      <td style="text-align:left">Conflicts</td>
      <td style="text-align:left">Can&apos;t run simultaneously to target units, the others will be stopped</td>
    </tr>
    <tr>
      <td style="text-align:left">Before/After</td>
      <td style="text-align:left">
        <ul>
          <li>Target units must be started before or after another one</li>
          <li>They&apos;ll also be stopped in the opposing order</li>
          <li>If these aren&apos;t set, they will be started/stopped in parallel</li>
          <li>If one is started and the other stopped, the shutdown is always done first</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

* You can also prevent spawning a unit without putting it in a fail state in order to adapt the unit to the kernel/runtime env, but i won't go into details

#### \[INSTALL\]

* Those are not used by systemd during runtime but by  the `enable` & `disable` commands of `systemctl`

<table>
  <thead>
    <tr>
      <th style="text-align:left">Option</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Alias</td>
      <td style="text-align:left">
        <ul>
          <li>Symbolic links will be created from those names to this unit</li>
          <li>Not supported by mount, slice, swap, and automount units</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">WantedBy / RequiredBy</td>
      <td style="text-align:left">Adds a symbolic link to this unit in the <code>.wants/</code> and <code>.requires/</code> directory
        of the target units</td>
    </tr>
    <tr>
      <td style="text-align:left">Also</td>
      <td style="text-align:left">Target units to install/uninstall when this one is</td>
    </tr>
  </tbody>
</table>

{% hint style="info" %}
* Read the uri & daemon man
* Check default instance
{% endhint %}

### Special  directories

* `/lib/systemd/system/` Base unit written here on installation, don't edit them
* `/run/systemd/system/` Files created at runtime
* `/usr/lib/systemd/user/` Unit files installed by packages, don't edit them
* `/etc/systemd/system/` Unit files that extend or overwrite a service
  * To extend or overwrite only some of the directives of a file, create a `<unit_name>.d/` dir and write them into a `.conf` file.
* The read order is as follows : `/usr/lib/systemd/user/ -> /run/systemd/system/ -> /etc/systemd/system/`
* All symbolic link files \(to others units\) in `<unit_name>.wants/`& `<unit_name>.requires/` will be added to the Wants & Requires directives of it's unit.

{% hint style="info" %}
Check the template thingy
{% endhint %}

## Systemctl

* Control the state of systemd
* Options :
  * `list-units`
  * `list-sockets`
  * `list-timers`
  * `start <unit>`
  * `stop <unit>`
  * `reload <unit>`
  * `restart <unit>`
  * `status <unit>`
  * ...

{% hint style="info" %}
There's a lot more but quite straightforward. I'll add it when i'll use it
{% endhint %}

## Sources

* man pages
* [https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)
* [https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)
* [https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)
* [https://www.linux.com/training-tutorials/understanding-and-using-systemd/](https://www.linux.com/training-tutorials/understanding-and-using-systemd/)

