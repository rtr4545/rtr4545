## **WRF Quickstart Guide**
### 1) Log-in to Derecho

`ssh -X -Y rtravarra@derecho.hpc.ucar.edu`

Enter password and authorize duo two-factor. You will be taken into your home directory: /glade/u/home/rtravarra
### 2) Create Working Directory
In your home directory, create a working directory for your project. In this example, the directory was named "EAS-5555."

`mkdirEAS-5555`

### 3) Obtain WRF and WPS Executables
- #### Locate WRF and WPS Executables
  - To obtain the WRF and WPS executables, navigate to the folder they are stored in.

    `cd /glade/work/wrfhelp/derecho_pre_compiled_code/`

     Use `ls` to view the different versions available. 
- #### Copy WRF and WPS Executables to Working Directory
  - In this example, WRF 4.1 and WPS 4.1 were used. Copy the WRF and WPS executables into your "EAS-5555" directory.

    `cp -rp wrfv4.1 ~/EAS-5555/`
    `cp -rp wpsv4.1 ~/EAS-5555/`

     Each command will take a few minutes. Navigate to your "EAS-5555" directory and use `ls` to ensure they were successfully copied.
### 5) Data
- #### Make a DATA directory
  - In your EAS-555 directory, create a "DATA" directory for your data files.
    
    `mkdirDATA`
    
- #### Obtain Data
    - Copy the data file link and place it into your "DATA" directory.

      `wget https://www2.mmm.ucar.edu/wrf/TUTORIAL_DATA/matthew_1deg.tar.gz`
      
      In this example, the data was copied from [[NCAR's Hurricane Matthew Tutorial](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/CASES/SingleDomain/ungrib.php)].
- #### Unpack the Data
    - Unzip and untar the data.

      `tar -xvf matthew_1deg.tar.gz`

      This will create a directory in you "DATA" Directory titled "matthew"

### 6) Link Vtable
   - In your WPS directory, "link" (i.e. [symbolic link](https://en.wikipedia.org/wiki/Ln_(Unix))) the "vtable" file.
     
     `ln -sf ungrib/Variable_Tables/Vtable.GFS Vtable`

     The file Vtable is just a lookup table that translates the variable names that GFS uses into something WRF will recognize.
### 7) Run link_grib.csh
   - Use the "c-shell" script called `./link_grib.csh` to link GRIB files to input files.
     
    ./link_grib.csh ../DATA/matthew/fnl

    Use `ls -alstr` to check if it was successful. You should see a list of files like so:

### 8) Edit Namelist
    - To edit "namelist.wps" use an in-line editor such as `vim`,`emacs`, or `nano`. In this example, I used `vim`.

      `vim namelist.wps` Then `i` to edit. Use `esc` to stop editing. `wq` will save your edits and quit.

### 9) Run ungrib.exe

    `./ungrib.exe`

    If ungrib ran successfully, you'll get the message below:

    !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

    !  Successful completion of ungrib.   !

    !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

### 10) Run geogrid.exe

We're now going to set up the model domain using geogrid.exe. To do this, make sure you're still in the wps directory (in this case, this would be accomplished by typing cd ~/EAS-5555/wpsv4.1/). Edit namelist.wps and save.

`vim namelist.wps`

Next, run geogrid.exe.

`./geogrid.exe`

If geogrid ran successfully, you'll get the message below:

`!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!  Successful completion of geogrid.        !
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!`

There should be a a new file in your WPS directory called `geo_em.d01.nc`

### 11) Interpolate data using metgrid.exe

Interpolate the input data onto our model (WRF) domain using metgrid.exe.

`./metgrid.exe`

If successful, you should see this:

`!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
 !  Successful completion of metgrid.  !
 !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!`

And you'll see the following files in your WPS directory:

`met_em.d01.2016-10-06_00:00:00.nc
 met_em.d01.2016-10-06_06:00:00.nc
 met_em.d01.2016-10-06_12:00:00.nc
 met_em.d01.2016-10-06_18:00:00.nc
 met_em.d01.2016-10-07_00:00:00.nc
 met_em.d01.2016-10-07_06:00:00.nc
 met_em.d01.2016-10-07_12:00:00.nc
 met_em.d01.2016-10-07_18:00:00.nc
 met_em.d01.2016-10-08_00:00:00.nc`

### 12) Get ready to run wrf.exe
  - Navigate to the directory where wrf.exe can be accessed.

     `cd ~/EAS-5555/wrfv4.1/test/em_real/`

  - Before you can run WRF, link the files you just created in the WPS         directory to the WRF directory:

    `ln -sf ~/EAS-5555/wpsv4.1/met_em.d01.2016-10*`

  - Edit namelist.input, save, and exit

    `vim namelist.input`

  - Run real.exe

  `./real.exe`

  This will take a minute or two if it is successful.

  - Use the linux command cat to spit out the contents of the error log to     your screen and make sure everything looks ok:

    `cat rsl.error.0000 `

    You should see the message below at the bottom of the output:

    `Assume Noah LSM input
     d01 2016-10-08_00:00:00 forcing artificial silty clay loam at    9    
     points, out of   8910
     d01 2016-10-08_00:00:00 Timing for processing          0 s.
     d01 2016-10-08_00:00:00 Timing for output          0 s.
     d01 2016-10-08_00:00:00 Timing for loop #    9 =          0 s.
     d01 2016-10-08_00:00:00 real_em: SUCCESS COMPLETE REAL_EM INIT`
### 13) Run wrf.exe
If real.exe ran successfully, you'll be able to run wrf.exe as follows:

`./wrf.exe`

### 14) WRF Model
  - You need to module load "ncview" before being able to use the command.

    `module load ncview`

  - Run ncview using the WRF file output

    `ncview wrfout_d01_2016-10-06_00:00:00`

    







      

