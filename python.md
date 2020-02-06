# Python

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
