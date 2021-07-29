# hpc-2021-2-fftw
Parallelization of the fast fourier transform (1d) in C.

## Table of contents
- [Authors](#authors)
- [Affiliation](#affiliation)
- [License](#license)
- [Objectives](#objectives)
- [Definition](#definition)
- [Requirements](#requirements)
- [Run](#run)
  * [Receiving signal](#receiving-signal)
  * [Processing signal (fftw)](#processing-signal--fftw-)
  * [Visualizing results](#visualizing-results)
    + [z axis projection](#z-axis-projection)
  * [Benchmarking with number of processes](#benchmarking-with-number-of-processes)
- [Example](#example)
  * [Receiving signal](#receiving-signal-1)
  * [Processing signal (fftw)](#processing-signal--fftw--1)
  * [Visualizing results](#visualizing-results-1)
    + [z axis projection](#z-axis-projection-1)
  * [Benchmarking with number of processes](#benchmarking-with-number-of-processes-1)
- [Introduction](#introduction)
- [Methodology](#methodology)
  * [Receiving signal sample](#receiving-signal-sample)
  * [Processing signal (fftw)](#processing-signal--fftw--2)
  * [Visualizing results](#visualizing-results-2)
    + [z axis projection](#z-axis-projection-2)
  * [Benchmarking with number of processes](#benchmarking-with-number-of-processes-2)
- [References](#references)
- [Next](#next)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



## Authors
Javier Navarro - <javiernavarro@comunidad.unam.mx>   
Juan Luis Ruiz - <juanluisruiz971@comunidad.unam.mx>   
Gustavo Zarate - <gustavo.zarate.389.cb84@gmail.com>   

## Affiliation
![UNAM ENES affiliation](images/UNAM_ENES.png)   
We are Bachelor of Science in Information Technologies ("Licenciatura Tecnologías para la Información en Ciencias") students at 6° semester (at editing date-time) at UNAM ("Universidad Nacional Autónoma de México") at the ENES ("Escuela Nacional de Estudios Superiores Unidad Morelia").

## License
MIT License

## Objectives
With this project we want to create a solution to divide multiple frequencies mixed in a wave parallelizing the process to get fast results.

## Definition
In this project, the Fast Fourier Transform is performed using high-performance computing techniques.
Compiled in C code with the MPI library to run multiple processes where the FFTW3 library is used to perform the transformation.

## Requirements
- MPI C implementation (we used [mpich library](https://www.mpich.org/)) MPICH 3.4.2
- [FFTW  library](http://www.fftw.org/) FFTW 3.3.9

## Run
### Receiving signal
_( Pending ... )_
### Processing signal (fftw)
```
mpicc fftw3-mpi_helloworld.c -lfftw3_mpi -lfftw3 -lm -lmpich -Wall -Ofast
```
```
mpiexec -n <number_of_processes> ./<executable_file> <number_of_samples> <input_file_path> <output_file_path>
```
The first command is run as it is. (You can run it one time and then reuse the executable file `a.out` for any input). Linking library files is required.

In the second command the first argument is the number of processes that are going to be generated, the second argument is the executable file, the third argument is the number of samples in the input file path which is the fourth argument, and the last argument is the file path where the fftw is going to be written.
### Visualizing results
#### z axis projection
```
python3 plot_samples.py <INPUT_FILE_PATH>
```
### Benchmarking with number of processes
_( Pending ... )_
## Example
### Receiving signal
_( Pending ... )_
### Processing signal (fftw)
```
mpicc fftw3-mpi_helloworld.c -lfftw3_mpi -lfftw3 -lm -lmpich -Wall -Ofast
mpiexec -n 4 ./a.out 8192 samples/samples_8192samples_97.6Mhz.iq out.iq
```
### Visualizing results
#### z axis projection
```
python3 plot_samples.py out.iq
```
![z axis projection example](images/z_proj_ex.png)
### Benchmarking with number of processes
_( Pending ... )_

## Introduction
_( Pending ... )_

## Methodology
### Receiving signal sample 
_( Pending ... )_
### Processing signal (fftw)
The executable file (`a.out`) compiled from `fttw3-mpi_helloworld.c` does this process.   
1. Declares necessary variables
    - N: number of samples in the input file (intialized with arguments)
    - INPUT_FILE_PATH: (initialized with arguments)
    - OUTPUT_FILE_PATH: (initialized with arguments)
    - p: variable where the 'plan' to perform the fftw is going to be
    - alloc_local: variable for number of complex numbers to allocate in each process
    - local_ni: variable for each of the processes' part of the input array
    - local_no: variable for each of the processes' output array
    - local_i_start: variable for the offset (number of complex numbers) to start reading the input array for each process
    - local_o_start: variable for the offset (number of complex numbers) to start writing the output array for each process
2. Removes previous output file (if it exists with the same current output path)
3. Initializes n processes with MPI (from now on everything runs in parallel in each process)
    1. Divides input array between all processes using `fftw_mpi_local_size_1d`. 
        - Initializes (gives value to):
            - local_ni
            - local_i_start
            - local_no
            - local_o_start
    2. Declares and allocates data to write and process part of the array in each process
        - local_in: array variable to write on the local (of each process) input array
        - local_out: array variable to write on the local (of each process) output array
    3. Makes fftw plan. The plan optimizes the process considering system and input specifications.
    4. Initializes the local arrays reading from the input file path. Unix library function `pread` is used to read in parallel the input file at a certain offset so each process reads just the part of the array that it needs.
    5. Executes the fftw (using the plan `p`).
    6. Writes the output to the output file path. This step is done in a serial manner (first process writes and the others wait, then the second writes and the others wait, then ...), though an upgrade would be writing in parallel with `pwrite` (tried and failed :( for now..).
4. Finalizes n processes with MPI and cleans
### Visualizing results
#### z axis projection
File with signal samples is read with python's numpy and graphed with matplotlib. The x axis is the real part of the complex number and the y axis is the imaginary part. Every point is a single sample.
### Benchmarking with number of processes
_( Pending ... )_

## References
[fftw](http://www.fftw.org/)      
[open MPI](https://www.open-mpi.org/)    
[C programming absolute beginner](https://sjceodisha.in/wp-content/uploads/2019/09/C-Programming-Absolute-Beginner-ssp.indian@gmail.com_.pdf)    
[hpc-2021-2](https://github.com/JavoJavo/hpc-2021-2)    
[Docs Oracle](https://docs.oracle.com/)    
[markdown-toc](https://ecotrust-canada.github.io/markdown-toc/)    

## Next
Create an open service with this process
