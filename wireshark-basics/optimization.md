# Wireshark Configuration Profiles and Packet Analysis

## Configuration Profiles

Edit -> Configuration Profiles  
or  
Right-click on Profile -> Manage Profile

### Creating a New Profile
1. Click the (+) Button.
2. When capturing packets, packet details and packet bytes are not needed.
3. Uncheck these options from the view to get a clearer packet list.

---

## Analyzing TCP Packets

For analyzing TCP packets, add the following fields as columns:
- Sequence Number  
- Acknowledgment Number  
- Received Window (Window)

To add:
Right-click on the field -> Apply as Column

### Window Field Explanation
"Window" indicates how many more bytes the receiver can accept before it needs to stop and send an ACK.  
This is essential for TCP analysis.

---

## Exporting Profiles

To export a Wireshark profile:
1. Edit -> Configuration Profiles
2. Select the profile you want to export
3. Click **Export**
4. Choose **Selected Personal Profile**
5. Give a name -> Save
6. A message “One Profile Exported” will appear

To remove a profile, click the (-) button.  
To add a profile back, import it (from ZIP file or directory).

---

## Exporting Packets

### Export Specific Packets
File -> Export Specific Packets -> File Name -> Displayed

You can manually select packets using:
- Hold **Ctrl** and click packets

Then go to:
File -> Export Specific Packets -> Selected packets only

Range options are also available.

---

## Splitting Large Capture Files

Large capture files can cause crashes.  
You can restrict how many packets go into a single file.

Steps:
1. Capture -> Options -> Output
2. Enable: Create a new file automatically
3. Choose **After -> Packets (e.g., 500)**
4. Destination -> Browse -> Name
   - Follows automatic naming pattern
5. Output format: pcapng or pcap
6. Compression: gzip (optional)
7. "After" options available:
   - Size
   - Seconds

---

## Automatically Stopping Packet Capture

To stop capturing packets automatically:
Capture -> Options -> Options -> Stop capture automatically after...

Try different available options to suit your requirement.

---

## Merging Capture Files

File -> Merge

You will get three options:
- **Prepend**
- **Merge chronologically**
- **Append**

The original files remain intact.

**Chronologically** means merging packets based on their timestamp.

### Alternative Method
- Ensure all capture files are in the same directory.
- Drag and drop one or more capture files into Wireshark.
- If multiple files are opened together, Wireshark merges them and orders packets by their timestamps (not by file selection order).

---

## Colouring Rules

View -> Colouring Rules

### Adding a Rule
Example:
- Name: ftp
- Filter: tcp.port==21

By default, white colour is applied to the filter.  
You can change Foreground and Background colours.

Filters can also be applied directly from the Colouring Rules window using the **"Apply as filter"** option.

To remove a colouring rule, click the (-) button.

---

## Resolving IP Addresses to Host Names

This feature is **disabled by default**.

To enable:
Edit -> Preferences -> Name Resolution -> Check "Resolve network (IP) addresses"

### Note
- When enabled, Wireshark performs **reverse DNS lookups** (resolves IP to domain names).
- This may generate additional DNS requests.

### DNS Request Explanation
A DNS request is a query sent by a web browser to a DNS server to resolve a domain name into an IP address.  
The main purpose of DNS is to translate human-readable domain names (like www.example.com) into IP addresses (like 192.0.2.1), which computers use to identify each other on a network.
