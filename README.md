# Analysis of GABA-B–Filamin A single-particle tracking data in CHO cells
Filamin A organizes γ aminobutyric acid type B receptors at the plasma membrane  
Marie-Lise Jobin, Sana Siddig, Zsombor Koszegi, Yann Lanoiselée, Vladimir Khayenko, Titiwat Sungkaworn, Christian Werner, Kerstin Seier, Christin Misigaiski, Giovanna Mantovani, Markus Sauer, Hans M Maric, Davide Calebiro,  
Nature Communications 14 (1), 1-14 (2023)

## System requirements
MATLAB R2018b
## Analysis requirements
### Movie Names
Movies of GABA-B and Filamin are recorded simultaneously as .tif files. GABA-B and Filamin A movies should have the same base name (example ‘MLJ1’) but with a suffix ‘-C1’ for GABA-B and a suffix ‘-C2’ for Filamin A.
##### Folders
The file ‘global_folders.m’ contains all the paths to the folders used in the analysis. The user should create all the folders specified in this file and update the paths in the file accordingly. All raw data should be stored in the folder specified as 'global_folders.rawfolder'.
##### Movie list
A file should be created that contains all movies organized by groups inside a MATLAB structure as follows:
movie_list.GROUP_NAME_1={'movie1','movie2','movie3'};
movie_list.GROUP_NAME_2={' movie4',' movie5',' movie6'};

To these movie names suffixes ‘-C1’ and ‘-C2’ will be attached automatically for GABA-B and Filamin A respectively.

###	Analysis parameters
The structure parameter contains all the parameters used for analysis. It can be loaded by calling the script ‘parameter_list.m’

### Coordinate alignment
For each channel, a .mat file should be created containing 2 ‘tform’ transformations (from C2 to C1 and from C1 to C2), the transformations should be named ‘t_piecewise_linear’ and ‘t_piecewise_linear_rev’ respectively. We recommend using a piecewise linear tform although an affine transformation could be used as well. 
The alignment matrix should be stored in the folder specified as ‘global_folders.rawfolder’.
## Trajectory analysis
### Detection and tracking
Detection and tracking are done using the u-track software, which can be downloaded here:
https://github.com/DanuserLab/u-track
>Jaqaman, K., Loerke, D., Mettlen, M. et al. Robust single-particle tracking in live-cell time-lapse sequences. Nat Methods 5, 695–702 (2008). https://doi.org/10.1038/nmeth.1237

The u-track parameters used in our study for detection and tracking can be found in the script called ‘detection_tracking_parameters.m’. 
After excluding trajectory coordinates that lie outside the region of interest, the output of u-track is converted to a custom format using the script ‘utrack_to_OBJ_v1.m’, which reorganises compound trajectories in u-track into separate objects. The script is adapted from Cocucci E. et al. The First Five Seconds in the Life of a Clathrin-Coated Pit. Cell 150, 3 495-507 (2012).
This will generate a file ‘{movie_name_basis}-C{n}_gui2_steps.mat’ in the folder ‘global_folders.rawfolder’.
This file contains several matrices. For each movie, a new field ‘IFO.calmatrix’ should be  appended to the IFO structure, which contains the file name of the alignment matrix (Example: IFO.calmatrix=’alignement_matrix’).

### Interaction analysis
The interaction analysis is performed using the script ‘MLJ_cycles_interaction.m’. The script computes the colocalization events between  C1 and C2 as described in Sungkaworn, T. et al. Single-molecule imaging reveals receptor-G protein interactions at cell surface hot spots. Nature 550, 543–547 (2017).
This script generates the following files in the folder ‘global_folders.rawfolder’:
‘{movie_name_basis}-C1{movie_name_basis}-C2_intmatrix2.mat’
‘{movie_name_basis}-C1{movie_name_basis}-C2_intmatrix_0pShiftX-0pShiftY-0fShiftT.mat’
Please cite 
>Sungkaworn, T. et al. Single-molecule imaging reveals receptor-G protein interactions at cell surface hot spots. Nature 550, 543–547 (2017).

### Analysis of diffusive states
The trajectories are first analysed with an algorithm that detects transient trapping events for each trajectory and each channel: 

The code for transient trapping detection can be downloaded here: 
https://github.com/YannLanoiselee/Transient_trapping_analysis

When using this code, please cite the paper 
>Lanoiselée, Y., Grimes, J., Koszegi, Z. & Calebiro, D. Detecting transient trapping from a single trajectory: A structural approach. Entropy 23, 1–16 (2021). 

Then, the information about transient trapping is combined with that of colocalization between C1 and C2 over time using the script “cycle_states_forced_or_not.m”. This generates a file ‘{movie_name_basis}-C{n}_list_states.mat’ for each movie and channel n in the folder ‘global_folders.state_analysis_folder’.

### Time-averaged MSD
The TAMSD is computed for each trajectory in C1 and C2 using the function ‘cycle_TAMSD_GABAB_Filamin.m’. For each trajectory, the analysis estimates the anomalous exponent α and the generalized diffusion coefficient D_α by fitting the TAMSD curve as a function of lag-time with the formula for the average TAMSD in 2 dimensions:

<img src="https://latex.codecogs.com/svg.latex?\Large&space;\langle%20\delta^2(\Delta,t)\rangle=4D_\alpha%20\Delta^\alpha+4\sigma^2"/>

The function generates a result matrix saved in ‘{movie_name_basis}-C{n}_MSD.mat’ containing the computed generalized diffusion coefficient and the anomalous exponent values in the first and second column, respectively.


