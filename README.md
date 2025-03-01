# Finding Goku (Crypto)
<hr>

## Problem Description

 Multiple Gokus appear, but only one is real. Spot subtle differences in power levels, speech, or battle knowledge to find the true one. Beware of trickster illusions and deceptive clones trying to mislead you. Use logic, observation, and special hints to uncover the real Saiyan warrior!

 ## Problem attached file code:
 ```python
import hashlib

def check_hex_data(hex1, hex2, start_string):
    if hex1 == hex2:
        return "Error: Even a Saiyan warrior knows that true strength lies in difference! The two inputs must not be identical."

    try:
        data1 = bytes.fromhex(hex1)
        data2 = bytes.fromhex(hex2)
    except ValueError:
        return "Error: Looks like you misfired a Ki blast! Invalid hex input detected."

    start_bytes = start_string.encode()

    if not (data1.startswith(start_bytes) and data2.startswith(start_bytes)):
        return "Error: These aren't true warriors! Both inputs must start with the legendary sign of 'GOKU' to proceed."

    def md5_hash(data):
        hasher = hashlib.md5()
        hasher.update(data)
        return hasher.hexdigest()

    hash1 = md5_hash(data1)
    hash2 = md5_hash(data2)

    if hash1 != hash2:
        return "Error: These warriors are impostors! They wear the same armor but their Ki signatures (MD5 hashes) don't match."

    try:
        with open("flag.txt", "r") as flag_file:
            flag = flag_file.read().strip()
        return f"🔥 You have found the real Goku! Your flag is: {flag}"
    except FileNotFoundError:
        return "Error: The Dragon Balls couldn't summon the flag! 'flag.txt' is missing."

if __name__ == "__main__":
    start_string = "GOKU"
    hex1 = input("Enter first hex data: ")
    hex2 = input("Enter second hex data: ")
    print(check_hex_data(hex1, hex2, start_string))
```

Connect to the server: `nc chals1.apoorvctf.xyz 5002`

## Solution
Look at the code carefully. The code is really very simple. After running the program it will ask for two hex values. But the hex value cann't be same . IF same the program will print error and we can't get the flag. Again we must start with 'GOKU' in hex in order to proceed with the flag. Now here comes the interesting part. At this point the program will check for the md5 hash value to see if those two values are the same. We know that each hash is unique but for the md5 hash there is something called md5 collision. It means that for two different values there can be the same hash value. To print the hash we perform a md5 collusion attack.
This challenge involves exploiting an MD5 collision. The provided Python script requires two distinct hex inputs that:

1.  Start with the ASCII representation of "GOKU".
2.  Have identical MD5 hashes.

To achieve this, we'll use `fastcoll` to generate an MD5 collision.

### Steps

1.  **Clone and build `fastcoll`:**

    ```bash
    git clone https://github.com/upbit/clone-fastcoll fastcoll
    cd fastcoll
    make
    cd ..
    mkdir bin
    cp fastcoll/fastcoll bin
    ```

2.  **Create a prefix file:**

    ```bash
    echo GOKU420 > pre.txt
    ```

    (Note: "420" is arbitrary; you can use other data after "GOKU".)

3.  **Generate the MD5 collision:**

    ```bash
    ./fastcoll pre.txt
    ```

    This will create `md5_data1` and `md5_data2`.

4.  **Verify the MD5 hashes:**

    ```bash
    md5sum md5_data1 md5_data2
    ```

    Confirm that the hashes are identical.

5.  **Convert the files to hex:**

    ```bash
    xxd md5_data1 > first 
    xxd md5_data2 > second
    ```
    This will produce two hex files containing the hex representation of the data.

6.  **Connect to the server and provide the hex data:**

    ```bash
    nc chals1.apoorvctf.xyz 5002
    ```
    You need to change the format of those files to be able to copy the hex values from the file

    Example (replace with your generated hex data):

    ```
    Enter first hex data: 474f4b553432300a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008085a6eeea782fa399cfb5848483f5da101aaa4aa359eeac4a223bd972768a82bbbdfde5dee7ad9a6b2877611022892db8f3ddaba439edc25960dcf180929598381ceaa30bdd439503654c3143771134020e62effb653626cb5a6eb7ec86dee730c5c658f6550440ca8f5d2fb994762ac78ec27be49ed52c0a266da1a593ef1a
    Enter second hex data: 474f4b553432300a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008085a6eeea782fa399cfb5848483f5da101aaacaa359eeac4a223bd972768a82bbbdfde5dee7ad9a6b28776110a2882db8f3ddaba439edc25960dc7180929598381ceaa30bdd439503654c3143771134020e626ffb653626cb5a6eb7ec86dee730c5c658f6550440ca8f5d2fb914772ac78ec27be49ed52c0a266d21a593ef1a
    ```

7.  **Retrieve the flag:**

    The server will output the flag:

    ```
    🔥 You have found the real Goku! Your flag is: apoorvctf{y0u_f0und_7h3_r34l_g0ku_hurr4yyy}
    ```
