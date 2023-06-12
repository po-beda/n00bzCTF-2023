# John Doe Strikes Again

The challenge presents a XORed conversation between John Doe and his assistant. After decrypt I know: The assistant asks John what he is listening to, and John responds by saying that he loves music and not to ask him that question again. 

```
$ python3 sherlock 31zdugxvkayexc4hzqhixxcfxb4y
```

[Sherlok](https://github.com/sherlock-project/sherlock) output that includes links to John Doe's Spotify and Telegram profiles.

By visiting John Doe's Spotify profile, the participant may be able to see the music that John has been listening to recently, which could potentially provide a clue.

The link provided is https://open.spotify.com/user/31zdugxvkayexc4hzqhixxcfxb4y. <br>
https://t.me/31zdugxvkayexc4hzqhixxcfxb4y is trash

<hr>

The next part of the challenge involves decoding an XORed string that the participant finds by following a Spotify. The participant is first given a text bytestring in python "104f1d22170b6e041845132e000e0a062d4f181c2b090c4d3c4f052a021a3b171345162c0f53174833001d4e30070c4f3250" -> "I have an amazing profile picture don't you think?"
Go to Discord and an indication that John Doe may be busy on n00bzctf.

To follow the clue about n00bzctf, the participant is instructed to visit the Wayback Machine at https://web.archive.org and search for archived versions of the n00bzctf website at https://ctf.n00bzunit3d.xyz/. By exploring the archives, the participant eventually discovers a page at https://ctf.n00bzunit3d.xyz/teams?page=2.

On this page, the participant finds a link to a strange page called "t0ps3cr3t" at https://ctf.n00bzunit3d.xyz/t0ps3cr3t. Visiting this page, the participant finds an XORed string `b"7_E!\x1b\x15 U)U\x1cp>\x17W\x06\x15\\x1b\rp\x1fV~\x14=[sT_\x00RZ:\x1cs\x15+P\x1ey\x017$t'+~\x1d_Fb\x1c".`

By using an XOR decoder:

**n00bz{n0_0n3_c4n_3sc4p3_MR.051N7,_n0t_3v3n_J0HN_D03!}**
