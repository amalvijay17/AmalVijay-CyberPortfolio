# Permissions

- `r` - Read  
- `w` - Write  
- `x` - Execute  
- `u` - User (owner whoever created the file/directory)  
- `g` - Group (permissions assigned to group of file/directory)  
- `o` - Other (everyone except `u` and `g`)  
- `a` - All (permissions of everyone)  
- `+` - Add permission  
- `-` - Remove permission  
- `=` - Make this the only permission  

**Syntax**
- `chmod ugoa+-= rwx filename`

**Examples**
- `chmod u+x somefile`  
- `chmod a+rw somefile`
