
Calibration of NMR chemical shifts calculation method with ORCA using 
experimental shifts and geometries of a test set of molecules.
Inspired by http://cheshirenmr.info/index.htm.

Usage: sigma_delta experimental_shifts.txt 'outs_dir' > method_sigma-delta.dat

The experimental_shifts.txt file contains information on the experimental 
chemical shifts for a set of molecules, along with the corresponding atom 
numbers in the calculated files. Data for 80 small molecules (Tantillo's Test 
Set) from http://cheshirenmr.info/AssociatedContent/Template.xls 
is provided in the ASCII files 1H_exp.txt and 13C_exp.txt located in the 
Tantillo_Test_set directory. The format of the experimental_shifts.txt file 
is described below.

The outs_dir directory must contain the ORCA output files with the 
chemical shift calculations. It must include all filenames mentioned in 
experimental_shifts.txt. The default file extension is .out, but a different 
one can be specified using the -ext=.extension option.

The geometries for the molecules from Tantillo's test set in XYZ format, 
optimized at the PBE0-D3/def2-TZVP/CPCM(Chloroform) level, are provided in the 
Tantillo_Test_set directory. Generating ORCA input files from these is 
straightforward and can also be done using the script:
../make_inp -method='functional basis' [-solv=CPCM_solvent] *.xyz
This script will create a directory named functional_basis_solvent 
and place the input files there.

The program prints to the console a table, formatted for easy import into Excel,
containing 4 columns:
 1. Calculated isotropic shielding constants (sigma)
 2. Experimental chemical shifts (delta)
 3. Molecule name
 4. The atom(s) to which the chemical shift corresponds

With the -stat option, only the statistics for the equations 
delta = slope*sigma + intercept and delta = reference - sigma are printed.
Please note: the first equation is not the same as the one used by Tantillo 
(his is delta = (sigma - intercept)/slope ).

Sample Protocol (Linux).
The following steps describe how to calibrate a new computational method, 
such as PBE0/def2-TZVP.
# Download scripts sigma_delta, make_inp and directory Tantillo_Test_set
chmod +x sigma_delta make_inp
cd Tantillo_Test_set
../make_inp -method='PBE0 def2-TZVP' -solv=CHCl3 *.xyz
cd PBE0_def2-TZVP_CHCl3
# Run ORCA inputs
cd ..
../sigma_delta -stat 1H_exp.txt Tantillo_Test_set
../sigma_delta -stat 13C_exp.txt Tantillo_Test_set

Format of the experimental_shifts.txt file:
  Empty lines and lines starting with # are ignored.
  There are several lines for each molecule.
  The first line is molecule_name. It must start in the first column.
  The outs_dir directory must contain a file molecule_name.out 
  with the NMR shieldings calculation.
  Each subsequent line must start with a space or tab and contain two fields.
  The first field is the atom number in the output file (starting from 1), 
  or a comma-separated list of atom numbers for which shifts should be averaged. 
  A range of numbers can be specified using a hyphen.
  The second field is the experimental chemical shift (ppm).
Example:
Norbornadiene
	1	75.20
	2,3	50.30
	4-7	143.20
