# picoCTF 2025 - Flag Hunters

**Category:** Reverse Engineering  
**Difficulty:** Easy  

---

## Description

The challenge provides a Python script and a Netcat service running the same script.  
The program prints song lyrics starting from `[VERSE1]`.

However, the flag is stored inside a hidden section called `secret_intro`, which is not printed during normal execution.

The goal is to make the program print the hidden intro containing the flag.

---

## Code Analysis

The script reads the flag and stores it inside `secret_intro`:

```python
flag = open('flag.txt', 'r').read()

secret_intro = \
'''Pico warriors rising...
''' + flag + '\n'
```

Execution starts from:

```python
reader(song_flag_hunters, '[VERSE1]')
```

This means the intro (which contains the flag) is never reached normally.

---

## How the Program Works

The song is split into lines:

```python
song_lines = song.splitlines()
```

The interpreter processes each line using:

```python
for line in song_lines[lip].split(';'):
```

This means a single line can contain multiple commands separated by `;`.

When the program encounters `CROWD`, it asks for user input:

```python
elif re.match(r"CROWD.*", line):
    crowd = input('Crowd: ')
    song_lines[lip] = 'Crowd: ' + crowd
```

The user input is inserted directly into the script.

---

## Vulnerability

Since lines are split using `;`, we can inject additional commands by including a semicolon in our input.

For example, if we enter:

```
test;RETURN 0
```

The line becomes:

```
Crowd: test;RETURN 0
```

After splitting, the interpreter processes:

- `Crowd: test`
- `RETURN 0`

The `RETURN` instruction changes the instruction pointer:

```python
elif re.match(r"RETURN [0-9]+", line):
    lip = int(line.split()[1])
```

This allows us to control where the program jumps.

---

## Exploitation

When prompted with:

```
Crowd:
```

We provide:

```
test;RETURN 0
```

This forces the program to jump to the beginning of the script, where `secret_intro` is located.

The hidden intro is printed, revealing the flag.

---

## Conclusion

The vulnerability exists because user input is directly inserted into a custom interpreter without sanitization.

Since the program splits lines using `;`, we can inject a `RETURN` command and redirect execution to the hidden section containing the flag.

This challenge demonstrates control flow manipulation through command injection in a simple interpreter.
