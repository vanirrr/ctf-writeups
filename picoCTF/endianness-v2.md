# picoCTF - Forensics - Medium
## Writeup:
I was given a file, it says it was recovered from a 32 bit system that organized the bytes in a weird way so i immediately got the idea of hex bytes  
After downloading the file i used exiftool on it just in case, and i was given this:  
![view](src/exiftool.png)  

Seeing the warning about jpeg, i went to check the jpeg signature bytes, then used xxd on the given file to see the first bytes. i found that the first signature bytes of the file have the same bytes of the jpeg signature, but in a scambled order  
Turns out the bytes are reversed in groups of 4!! meaning each group of 4 hex values is reversed as you can see:  
![view](src/bytes.png)

Afterwards, i made a python script that reads the file's bytes and reverses bytes in groups of 4:  
```
import sys

def reverse_bytes_in_groups_of_4(input_file, output_file):
    with open(input_file, 'rb') as f:
        data = f.read()

    # Process in groups of 4 bytes
    recovered_data = bytearray()
    for i in range(0, len(data), 4):
        group = data[i:i+4]
        recovered_data.extend(group[::-1])  # reverse the group

    # Write the recovered file
    with open(output_file, 'wb') as f:
        f.write(recovered_data)

    print(f"Recovered file saved as: {output_file}")

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python recover_jpeg.py <input_file> <output_file>")
        sys.exit(1)

    input_file = sys.argv[1]
    output_file = sys.argv[2]
    reverse_bytes_in_groups_of_4(input_file, output_file)

```
Running the script i get a jpg output, opening it shows this image with the flag:  
![view](src/recovered.jpg)
