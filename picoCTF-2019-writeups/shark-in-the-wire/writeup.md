# PicoCTF 2019

## Shark-in-the-wire (Forensics)

Used wireshark to solve this. The packet capture file has all the list of packets it captured. Filtering the packet list on the basis of certain protocol didn't yield any result. Rather, did a filtering on the basis of destination IP address (or address for some othe type of address) and then for each filtered list of packet, used wireshark stream feature to build the ASCII string content and get the whole string which was transfered as several packets. The packet capture sure did had lot of noises, but finally packet stream for destination IP 10.0.0.12 gave the flag. 
