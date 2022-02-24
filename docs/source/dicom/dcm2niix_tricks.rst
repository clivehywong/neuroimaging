Useful Options of dcm2niix 
###########################

Output Naming (-f)
*******************
dcm2niix file name options: default '%f_%p_%t_%s'

Useful ones:

%s=series number, 

%t=time, 

%e=echo number, <Maybe useful for fieldmap, which contained multi-echo data>

%d=description, 

%p=protocol, 

%z=sequence name, 

%d, %p, %z should tell what is scanned with the sequence. It actually depends on scanner brand and protocol setup. 

Not Useful:

%a=antenna (coil) name, 

%b=basename, %c=comments, 

%f=folder name, 

%g=accession number, 

%j=seriesInstanceUID, 

%k=studyInstanceUID, 

%m=manufacturer, 

%o=mediaObjectInstanceUID, 

%r=instance number, 

%u=acquisition number, 

%v=vendor, 

%x=study ID; 

Never:

%i=ID of patient, 

%n=name of patient, 



Examples
=========

## Generate ONLY json files (-b o), named with useful headers. Useful to decide how to formulate the final conversion.

dcm2niix -b o -i y -o . -f json/sub-001/b-%b--c-%c--d-%d--e-%e--f-%f--p-%p_--r-%r_--s-%s_--t-%t--z-%z sub-001


## Convert DICOMs, sort by sequence description (-f ..%d..), ignore localisers (-i y), named with sequence number (-f ..%s..) and sequence description (-f ..%d..)

dcm2niix -b o -i y -o . -f bids/sub-0001/%d/%s_%d dcm/sub-0001


## Sort DICOM into folder named "dcmsorted" without conversion (-r y), ignore localisers (-i y), create subject folder (-f %f..), create sequence folder named with sequence number (-f ..%s..) and sequence description (-f ..%d..).

mkdir -p dcmsorted

dcm2niix -r y -i y -o dcmsorted -f %f/%s_%d/%b sub-001
