# CTF Challenge Writeup: Down The Rabbit Hole

## Challenge Information

- Name: Down The Rabbit Hole

## Challenge Analysis

Sadly I haven't made a lot of notes on this challenge. From a 'forgotten' source, I got the docker files.
These files had some obfuscated data. I've made a script to solve the flag, it is show below.

*I'm uploading my custom script since nobody has released a writeup on CTF Time.*

## Solution

### Script:

```
#!/usr/bin/python
import os
import json
import tarfile
from time import sleep
from base64 import b64decode

def get_parent(current):
    with open(attack_path + current + '/json') as f:
        parent = json.loads(f.read())['parent']
        f.close()
    return parent

def get_door(current, flag):
    current_tar = tarfile.open(attack_path + current + '/layer.tar')
    try:
        current_tar.extract('door', '/tmp')
    except KeyError:
        current_tar.close()
        return ''
    current_tar.close()
    with open('/tmp/door') as f:
        flag = f.read()
        f.close()
        os.remove('/tmp/door')
    return flag.rstrip('\r\n')

if __name__ == '__main__':
    print('[+] Starting...')
    flag = ""
    attack_path = os.getcwd() + '/extracted/rabbit_hole/'
    print('[+] Targeting: ' + attack_path)
    with open(attack_path + 'repositories') as f:
        current = json.loads(f.read())['wonderland']['latest']
        f.close()
    print('[+] Found:\t' + current)
    while True:
        try:
            print('[+] Loading:\t' + current)
            flag += get_door(current, flag)
            print('[+] Flag:\t' + flag)
            current = get_parent(current)
        except KeyError:
            print(f'''
FLAG:\t{flag}
DONE:\t{b64decode(flag[::-1])}
''')
quit()
```

## Writeup Author

- Twitter: [@Cyber8RU7U5](https://twitter.com/Cyber8RU7U5)

