---
title: "Random Parcelization"
date: 2020-02-14T15:30:49+01:00
lastmod: 2020-02-14T15:30:49+01:00
publishdate: 2020-02-14T15:30:49+01:00
description: "Random Parcelization"
weight: 10
---

# Random Parcelization

Considering files DKTatlas40 (atlas) for marco parcelization and ic4 micro parcelization (uniform) we map the brain and we found a number of randomic variance of this parcelization

# Phase 2

In order to prepare data and parcelyze randomly patient data you need to perform a series of actions using FreeSurfer scripts.
Recall to setup SUBJECTS_DIR and FREESURFER_HOME environment variables to work with FreeSurfer.
The actions are:
- merge all labels in a single label per hemisphere
- create an annotation (label + colortable) from merged labels
- apply this label + coloratab to each patient to create the original labelling
- 

# Subjects directory schema

```
.
└── subjects
    └── subject
        ├── label
        |   ├── [colortable].ctab: colortable file
        |   └── [hemisphere].[atlas].annot: informations about vertices, colortable, labels
        └── surf
            ├── [hemisphere].sphere.reg: geometry, 3D vertex and faces of the inflated surface
            └── [hemisphere].orig: geometry, 3D vertex and faces of the brain surface
```

## Merge Labels

This step use the command (mri_mergelabels)[https://surfer.nmr.mgh.harvard.edu/fswiki/mri_mergelabels]. This command merges all labels (.label) from an hemisphere (lh or rh) into a single .label file.
TODO explain what label need to be taken and what those labels represents

Example:
```
mri_mergelabels -i label1 -i label2 -i label3
    -o output.label
    -d directory                                # all labels in a directory
```

mri_mergelabels <all label ic4 from lh> -> lh.label

## Label to Annotation
This step use the label file generated at previous step (one for each hemisphere), merge it with color informations and produce an annotation file .annot.
The FreeSurfer command used is (mris_label2annot)[https://surfer.nmr.mgh.harvard.edu/fswiki/mris_label2annot]

Example:
```
mris_label2annot 
    --s [patient] 
    --h [hemisphere]
    --ctab [colortable].ctab 
    --a [suffix] 
    --l lh.unknown.label 
    --l lh.bankssts.label
    --l lh.caudalanteriorcingulate.label 
    # (obscure) --nhits nhits.mgh
```

or with all labels in a directory:

```
mris_label2annot 
    --s [patient] 
    --h [hemisphere]
    --ctab [colortable].ctab 
    --a [suffix] 
    --ldir [label directory]
    # (obscure) --nhits nhits.mgh
```

## Surface to Surface
Resamples one CorticalSurface onto another using (mri_surf2surf)[http://freesurfer.net/fswiki/mri_surf2surf].

Example:
```
mri_surf2surf 
    --srcsubject [source patient] \
    --trgsubject [target patient] \
    --hemi [hemisphere] \
    --sval-annot [source annotation].annot \
    --tval [target annotation].annot
```
exmaple
```
mri_surf2surf 
    --srcsubject fsaverage
    --trgsubject bert
    --hemi lh \
    --sval-annot aparc.a2005s.annot \
    --tval lh.target.aparc.annot
```

The script uses the files [hemisphere].sphere.reg and [hemisphere].orig of the source and of the destination subject together with label [hemisphere].[annotationfile].annot to produce an output annotation file on the target surface.

## Label to paziente originale

MCFLIRT is an intra-modal motion correction tool designed for use on fMRI time series and based on 
optimization and registration techniques used in FLIRT, a fully automated robust and accurate tool 
for linear (affine) inter- and inter-modal brain image registration.

subject
directory = subject dirs
lh or rh
path_dati
functional = 

mcflirt 
    -in [paziente]/session_1/rest_1/rest.nii.gz \
    -out [paziente]/func/rest.nii.gz \
    -cost mutualinfo \
    -refvol 1 \
    -mats \
    -plots

## Low resolution ribbon




#echo "*****************************************************************************************"
#echo "******************************Create_low_resolution_ribbon****************************"
#echo "*************************************************************************************"

d=$SUBJECTS_DIR/$subjid1/mri
## back up original ribbon and aseg files
mkdir $d/1mm; mv $d/*ribbon* $d/aseg.mgz $d/brain.mgz -t $d/1mm; 

#echo mri_convert $d/1mm/aseg.mgz -o $d/aseg.mgz --reslice_like ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz --resample_type nearest

$FREESURFER_HOME/bin/mri_convert ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.nii.gz ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.nii
$FREESURFER_HOME/bin/mri_convert ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.nii ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz

cp ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz ${SUBJECTS_DIR}/${subjid1}/mri/brain.mgz
$FREESURFER_HOME/bin/mri_convert $d/1mm/brain.mgz -o $d/brain.mgz --reslice_like ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz --resample_type nearest
$FREESURFER_HOME/bin/mri_convert $d/1mm/aseg.mgz -o $d/aseg.mgz --reslice_like ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz --resample_type nearest
$FREESURFER_HOME/bin/mris_volmask_novtk --label_left_white 2 --label_left_ribbon 3 --label_right_white 41 --label_right_ribbon 42 --save_ribbon --save_distance $subjid1
mkdir $d/3mm; mv $d/*ribbon* $d/aseg.mgz $d/brain.mgz -t $d/3mm
mv $d/1mm/*ribbon* $d/1mm/aseg.mgz $d/1mm/brain.mgz -t $d


#rm -r ${SUBJECTS_DIR}/${subjid1}/func/splitted/

#####################################
#####################################

#echo "mri_label2vol....."


mkdir $SUBJECTS_DIR2/volumi_paziente_originali
mkdir $SUBJECTS_DIR2/volumi_paziente_originali/$directory


#echo "tkregister2....."
$FREESURFER_HOME/bin/tkregister2 --mov ${SUBJECTS_DIR}/${subjid1}/mri/T1.mgz --noedit --s ${subjid1} --regheader --reg ${SUBJECTS_DIR}/${subjid1}/mri/register.dat
$FREESURFER_HOME/bin/tkregister2 --mov ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz --noedit --s ${subjid1} --regheader --reg ${SUBJECTS_DIR}/${subjid1}/mri/register_functional1.dat
#echo "Using an opportune register.dat"
export register_command=`echo --reg ${SUBJECTS_DIR}/${subjid1}/mri/register.dat`
export register_command_functional=`echo --reg ${SUBJECTS_DIR}/${subjid1}/mri/register_functional1.dat`

 
FILES=$SUBJECTS_DIR2/label_paziente_originali/$directory/parcels.label/*
for f in $FILES
do
#echo "Processing $f file..."
#echo "****************************************************************************************"
#echo "******************************Processing file****************************"
#echo "*************************************************************************************"
inputf=`basename $f`
#echo $inputf 
filename="${inputf%.*}"
#echo $filename

#####################################

$FREESURFER_HOME/bin/mri_label2vol --label ${f} --temp ${SUBJECTS_DIR}/${subjid1}/func/splitted_0000.mgz --subject ${subjid1} --hemi ${lh_rh} --surf white  --fillthresh 0.5 --proj frac 0 1 0.01 --o $SUBJECTS_DIR2/volumi_paziente_originali/$directory/${filename}.nii.gz $register_command_functional
#echo "mri_label2vol done"
$FREESURFER_HOME/bin/mri_binarize --dilate 1 --erode 1 --i $SUBJECTS_DIR2/volumi_paziente_originali/$directory/${filename}.nii.gz --o $SUBJECTS_DIR2/volumi_paziente_originali/$directory/${filename}.nii.gz --min 1
#echo "mri_binarize done"
$FREESURFER_HOME/bin/mris_calc -o $SUBJECTS_DIR2/volumi_paziente_originali/$directory/${filename}_ribb_ref.nii.gz $SUBJECTS_DIR2/volumi_paziente_originali/$directory/${filename}.nii.gz mul ${SUBJECTS_DIR}/${subjid1}/mri/3mm/${lh_rh}.ribbon.mgz
#echo "mris_calc done"
#####################################
done