# How many asterisks can be put?

In C and C++, the asterisk (`*`) is used to declare or dereference a pointer variable.

By the way, did you know that you can prefix a function call with as many asterisks as you like?

Here is the simple and valid C program.

```cpp
#include <stdio.h>

int main(){
  printf("Hello world!\n");
}
```

Here is a valid C program too.

```cpp
#include <stdio.h>

int main(){
  (*printf)("Hello world!\n");
}
```

Here is also a valid one.

```cpp
#include <stdio.h>

int main(){
  (******printf)("Hello world!\n");
}
```

This program with 100 asterisks is also a perfect C program.

```c
#include <stdio.h>

int main(){
  (
   **********
   **********
   **********
   **********
   **********
   **********
   **********
   **********
   **********
   **********
   printf)("Hello world!\n");
}
```

You see this and you're wondering how many asterisks you can add? Let's find out.

This is a Ruby script that prepends the specified number of asterisks to the printf function.

```rb
def check(n)
  s = "*"*n
  open("test.c","w") do |f|
  f.puts <<EOS
#include <stdio.h>
int main(){
(#{s}printf)("Hello World!\\n");
}
EOS
end
  return system("clang test.c")
end

check(ARGV[0].to_i)
```

You can use like this.

```sh
ruby check.rb 10
```

It will generate a following source codes.

```c
#include <stdio.h>
int main(){
(**********printf)("Hello World!\n");
}
```

Let's try it with a thousand asterisks.

```sh
ruby check.rb 1000
```

No problem. Then try with ten thousand.

```sh
ruby check.rb 10000
PLEASE submit a bug report to https://bugs.llvm.org/ and include the crash backtrace, preprocessed source, and associated run script.
Stack dump:
(snip)
clang-12: error: unable to execute command: Segmentation fault (core dumped)
clang-12: error: clang frontend command failed due to signal (use -v to see invocation)
```

Clang died with SIGSEGV.

Clang was ok with 1000 asterisks and died with 10,000 asterisks. So there must be a limit somewhere in there. Let's check it out with a binary search.

```rb
def check(n)
  s = "*"*n
  open("test.c","w") do |f|
  f.puts <<EOS
#include <stdio.h>
int main(){
(#{s}printf)("Hello World!\\n");
}
EOS
end
  if system("clang test.c 2> /dev/null")
    puts "#{n} OK"
    false
  else
    puts "#{n} NG"
    true
  end
end

(1000..10000).bsearch do |n|
  check(n)
end
```

Here is the execution result.

```sh
$ ruby search.rb
5500 NG
3250 NG
2125 NG
1562 OK
1844 NG
1703 NG
1633 OK
1668 NG
1651 OK
1660 NG
1656 OK
1658 NG
1657 OK
```

Now, we have new knowledge. We can put up to 1657 asterisks (environment dependent).

By the way, let's try it with GCC. Here is a check script.

```rb
def check(n)
  s = "*"*n
  open("test.c","w") do |f|
  f.puts <<EOS
#include <stdio.h>
int main(){
(#{s}printf)("Hello World!\\n");
}
EOS
end
  return system("gcc test.c")
end

check(ARGV[0].to_i)
```

Let's try from 1000.

```sh
ruby check_gcc.rb 1000
```

No problem. Next, 10,000.

```sh
ruby check_gcc.rb 10000
```

Well, how about 100,000?

```sh
ruby check_gcc.rb 100000
```

Seriously? Then, 1,000,000.

```sh
$ ruby check_gcc.rb 1000000
gcc: internal compiler error: Segmentation fault signal terminated program cc1
Please submit a full bug report,
with preprocessed source if appropriate.
See <http://bugzilla.redhat.com/bugzilla> for instructions.
```

Oops, gcc dided with a million of asterisks.

So, If you want to use ten thousand asterisks, use GCC.
