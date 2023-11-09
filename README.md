# precord

[![crates.io](https://img.shields.io/crates/v/precord.svg)](https://crates.io/crates/precord)
[![docs.rs](https://docs.rs/precord/badge.svg)](https://docs.rs/precord)
![Minimum rustc version](https://img.shields.io/badge/rustc-1.64+-green.svg)

Command line tool for recording process or system performance data.

<img src="asset/Chrome.svg" width="480"></img>

## Installation

Install with `cargo`:

```shell
cargo install precord
```

## Usage

```shell
precord -p 1203 -o result.svg
```

### Options

```shell
precord -h
```

- `-c / --category` - categories for recording, possible values:
  - `cpu` - CPU usage of process
  - `mem` - Memory usage of process
  - `alloc` - Allocation of process
  - `gpu` - GPU usage of process
  - `vram` - VRAM usage of process
  - `fps` - Frame rate of process
  - `net_in/net_out` - Network recv/send of process
  - `disk_read/disk_write` - Disk read/write of process
  - `kobject` - Kernel object count of process
  - `thread` - Thread count of process
  - `sys_cpu` - CPU usage of system
  - `sys_cpu_freq` - CPU frequency of system
  - `sys_cpu_temp` - CPU temperature of system
  - `sys_gpu` - GPU usage of system
  - `sys_power` - Power usage of system
  - `sys_npu_power` - Power usage of npu
- `-p / --process` - ID of processes
- `--name` - Name of processes
- `-o / --output` - Specify the output file, possible extensions:
  - `.svg`
  - `.html`
  - `.json`
  - `.csv`
- `-i / --interval` - Interval for recording
- `-n` - Count for recording
- `--time` - Time limit for recording, e.g., `--time 1h30m59s`
- `-r / --recurse-children` - Flag to recurse child processes
- `--skip` - Number of skip records
- `--gpu-calc` - Gpu calculation, possible values:
  - `max`
  - `sum`

### Command Mode
Type `:` during recording will enter the command mode, and press `Esc` will back to recording. The supported commands are:
- `w` - Write(Save) output
- `q` - Exit
- `wq` - Combination of `w` and `q` 
- `time` - modify time limit for recording

### Sub Commands

- `thread-list <PID>` - List threads and cpu usage

## precord-core

A library for retrieving process and system performance data.

```rust
use precord_core::{Features, GpuCalculation, System};
use std::thread;
use std::time::Duration;

fn main() {
  let mut system = System::new(Features::PROCESS || Features::GPU, [1203]).unwrap();
  thread::sleep(Duration::from_secs(1));
  system.update();

  if let Some(cpu_usage) = system.process_cpu_usage(1203) {
    println!("Process({}) %CPU: {:.2}%", 1203, cpu_usage)
  }
  
  #[cfg(target_os = "windows")]
  if let Some(gpu_usage) = system.process_gpu_usage(1203, GpuCalculation::Max) {
    println!("Process({}) %GPU: {:.2}%", 1203, gpu_usage)
  }
  
  if let Some(sys_gpu_usage) = system.system_gpu_usage(GpuCalculation::Max) {
    println!("System %GPU: {:.2}%", sys_gpu_usage);
  }
}
```

## Supported Platforms

|                      | macOS              | Windows            | Linux              |
|----------------------|--------------------|--------------------|--------------------|
| cpu                  | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| mem                  | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| alloc                |                    | :white_check_mark: |                    |
| gpu                  |                    | :white_check_mark: |                    |
| vram                 |                    | :white_check_mark: |                    |
| fps                  | :white_check_mark: | :white_check_mark: |                    |
| net_in/net_out       | :white_check_mark: | :white_check_mark: |                    |
| disk_read/disk_write | :white_check_mark: |                    |                    |
| kobject              | :white_check_mark: | :white_check_mark: |                    |
| thread               | :white_check_mark: | :white_check_mark: |                    |
| sys_cpu              | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| sys_cpu_freq         | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| sys_cpu_temp         | :white_check_mark: | :white_check_mark: |                    |
| sys_gpu              | :white_check_mark: | :white_check_mark: |                    |
| sys_power            | :white_check_mark: | :white_check_mark: |                    |
| sys_npu_power        | :white_check_mark: |                    |                    |

## Privileges

|                                                    | macOS                          | Windows       | Linux |
|----------------------------------------------------|--------------------------------|---------------|-------|
| cpu                                                |                                |               |       |
| mem                                                |                                |               |       |
| alloc                                              |                                |               |       |
| gpu                                                |                                |               |       |
| vram                                               |                                |               |       |
| fps                                                | Administrator + get-task-allow | Administrator |       |
| net_in/net_out                                     |                                | Administrator |       |
| disk_read/disk_write                               |                                |               |       |
| kobject                                            |                                |               |       |
| thread                                             |                                |               |       |
| sys_cpu                                            |                                |               |       |
| sys_cpu_freq                                       | Administrator                  |               |       |
| sys_cpu_temp                                       |                                |               |       |
| sys_gpu                                            |                                |               |       |
| sys_power                                          |                                |               |       |
| sys_npu_power                                      | Administrator                  |               |       |
| system processes<br/>(WindowServer, dwm.exe, etc.) | Administrator                  | Administrator |       |


## TODO

## Related projects

- Rust
  - [sysinfo]
  - [heim]
  - [rust-psutil]
- Go
  - [gopsutil]
- Python
  - [psutil]

[sysinfo]: https://github.com/GuillaumeGomez/sysinfo
[heim]: https://github.com/heim-rs/heim
[rust-psutil]: https://github.com/rust-psutil/rust-psutil
[gopsutil]: https://github.com/shirou/gopsutil
[psutil]: https://github.com/giampaolo/psutil
