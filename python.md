# Python

#### Tempalte for command line application with subcommands

```python
import argparse
import logging
import sys


class UserError(Exception):
    pass


def parse_args():
    parser = argparse.ArgumentParser()
    subparsers = parser.add_subparsers(dest='command', required=True)

    subparsers.add_parser('foo')

    return parser.parse_args()


def foo_command():
    pass


def main(command, **kwargs):
    commands = {
        'foo': foo_command}

    commands[command](**kwargs)


def entry_point():
    logging.basicConfig(level=logging.INFO, format='%(message)s')

    try:
        main(**vars(parse_args()))
    except UserError as e:
        logging.error(f'error: {e}')
        sys.exit(1)
    except KeyboardInterrupt:
        logging.error('Operation interrupted.')
        sys.exit(130)
```

#### Convert a file size in bytes into a human-readable string

```python
def format_size(size):
    if size < 1000:
        return '{} bytes'.format(size)

    for unit in 'KMGTPEZY':
        size = size / 1000

        if size < 10:
            return '{:.2f} {}B'.format(size, unit)
        elif size < 100:
            return '{:.1f} {}B'.format(size, unit)
        elif size < 1000 or unit == 'Y':
            return '{:.0f} {}B'.format(size, unit)


print(format_size(9))
print(format_size(89))
print(format_size(789))
print(format_size(6789))
print(format_size(56789))
print(format_size(456789))
print(format_size(3456789))
print(format_size(23456789))
print(format_size(123456789))
print(format_size(1234567890))
print(format_size(12345678900))
print(format_size(123456789000))
print(format_size(2 ** 100))

# 9 bytes
# 89 bytes
# 789 bytes
# 6.79 KB
# 56.8 KB
# 457 KB
# 3.46 MB
# 23.5 MB
# 123 MB
# 1.23 GB
# 12.3 GB
# 123 GB
# 1267651 YB
```

#### Implement `__eq__()` and `__hash__()` by providing a single `_hashable_key()` method

```python
class Hashable:
    def __eq__(self, other):
        return type(self) is type(other) and self._hashable_key() == other._hashable_key()
    
    def __hash__(self):
        return hash(self._hashable_key())

    def _hashable_key(self):
        raise NotImplementedError()
```

#### Implement `__lt__()` and co. by providing a single `_hashable_key()` method

```python
@total_ordering
class Ordered(Hashable):
    def __lt__(self, other):
        if type(self) == type(other):
            return self._hashable_key() < other._hashable_key()
        else:
            return NotImplemented
```

#### File-like object which hashes data written to it. E.g. useful in conjunction with `shutil.copyfileobj()`

```python
class _HashFile(io.RawIOBase):
    """
    A simple file-like object which calculates a hash of the data written to
    it.
    """

    def __init__(self, hash=hashlib.sha256):
        self.hash = hash()

    def write(self, b):
        self.hash.update(b)
```

#### Context managers to atomically replace a file

```python
import contextlib
import os
import pathlib
from typing import ContextManager, BinaryIO


@contextlib.contextmanager
def atomically_replaced_path(dest_path: pathlib.Path) -> ContextManager[pathlib.Path]:
    temp_path = dest_path.with_name(dest_path.name + '~')

    yield temp_path

    temp_path.rename(dest_path)


@contextlib.contextmanager
def atomically_replaced_file(dest_path: pathlib.Path) -> ContextManager[BinaryIO]:
    with atomically_replaced_path(dest_path) as temp_path:
        with temp_path.open('wb') as file:
            try:
                yield file
            except Exception:
                temp_path.unlink()

                raise

            os.fsync(file.fileno())


def atomically_replace_symlink(dest_path: pathlib.Path, target_path: pathlib.Path):
    with atomically_replaced_path(dest_path) as temp_path:
        temp_path.symlink_to(target_path)
```
