# pestilence

This project is the continuation of [famine](https://github.com/rcabezas29/famine). The purpose is to adding some hiding methods.

- Anti-Debugging
- Specific process evader
- Obfuscation

### Anti-Debugging

By calling the syscall `ptrace`, the virus can detect if it is being debugged as it is using this syscall. As this syscall can only be used once by process and the debuggers use it, it detects it and jumps to the end of the program doing nothing.

### Specific process evader

Every process running in Linux systems it present in the `/proc` folder. The virus look for a specific process name over all the processses folders and, if it finds that specific process name, exits the program doing nothing so that it can not be detected.

### Obfuscation

For obfuscation several techniques have been used. It is important that, in case someone analyzes the virus, he/she could not understand it easily. The program should work the same but the code must look like if it is doing other things.

A lot of useless code has been added.

Examples:
```assembly
nop		               ; useless instruction


xor rax, 1
xor rax, 1             ; XOR gates, if applied twice the same, do nothing


mov rax, SYS_GETUID
syscall                ; useless syscalls that do not alter the code


_write_an_important_message:
	mov rax, SYS_WRITE
	mov rdi, 1
	lea rsi, [r15]
	mov rdx, 0
	syscall            ; writes with len 0


xor rdi, rdi
_important_loop:
	inc rdi
	mov rsi, 0xaf01
	inc rdi
	xor rax, rax
	imul rax, rax, 1
	dec rdi
	add rdi, 4
	nop
	cmp rdi, 0x001001
	jl _important_loop
	imul rax, rax, 1   ; more useless functions
```

#### Packing

Another technique used for obfucation was *packing*. By a [XOR cipher](https://en.wikipedia.org/wiki/XOR_cipher), the code is injected encrypted. The first part of the program will apply the same XOR cipher so it will decrypt the code for its previous execution.

This proceess makes the code to be crypted if anyone with any program wants to analyze it.

## Usage

As there is a `.devcontainer`, you can open the project with your VSCode with the appropiate extension and the Docker container will deploy automatically.

Alternatively, you can deploy it the hard mode:

```bash
docker build -t pestilence .
docker run -v $(pwd):/root/pestilence -it pestilence
```

Inside the container:

```bash
make && ./build/pestilence
```

If you want to see the syscalls and a simple test we have done:

```bash
make run
```

or to debug it:

```bash
make g
```

## Testing

We have added some [testing](./test/test.sh). This can be executed with:
```bash
make test
```
