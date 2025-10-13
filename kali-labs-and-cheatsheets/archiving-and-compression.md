# Archiving and Compression

## Archiving (tar flags)
- `tar -c` — Create archive  
- `tar -r` — Append to archive  
- `tar -t` — List contents of archive  
- `tar -x` — Extract archive  
- `tar -v` — Verbose  
- `tar -f <file>` — File to use

## Compression
- `gzip` — Classic compression (`-d` to decompress)  
- `bzip2` — More compression than gzip (`-d` to decompress)

## Examples
- Create tar from files:  
  - `tar cvf archive.tar file1 file2 dir1 dir2`
- List content of archive:  
  - `tar tvf archive.tar`
- Add files or directory to archive:  
  - `tar rvf archive.tar file1`  
  - `tar rvf archive.tar dir1`
- Extract file or directory:  
  - `tar xvf archive.tar`  
  - `tar xvf archive.tar file1`  
  - `tar xvf archive.tar dir1/subdir1`
- Extract in different directory:  
  - `tar xvf archive.tar -C /tmp/extracted`  
  - `tar xvf archive.tar -C ~/Desktop`
- Compress (gzip):  
  - `tar zcvf compressed.tar.gz file1 file2`
- Compress (bzip2):  
  - `tar jcvf compressed.tar.bz2 file1 file2`
- List compressed archives:  
  - `tar ztvf compressed.tar.gz`  
  - `tar jtvf compressed.tar.bz2`
- Extract from compressed archives:  
  - `tar zxvf compressed.tar.gz file1`  
  - `tar jxvf compressed.tar.bz2 file1`
- Extract everything:  
  - `tar zxvf compressed.tar.gz`  
  - `tar jxvf compressed.tar.bz2`
