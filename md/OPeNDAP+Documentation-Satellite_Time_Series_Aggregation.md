[\<-- Back](Use_cases_for_swath_and_time_series_aggregation "wikilink")

**Point Of Contact:** *James*

## Description

Satellite_Time_Series_1

## Goal

The goal of this use case is, like many of its other parts, the same as
the Satellite Swath Data use case. The primary difference is that this
use case covers Time Series data.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

## Activity Diagram

<font size="-2" color="green">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font size="-2" color="green">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

Where to find sample data for this use case:

- <http://nsidc.org/opendap/GLAS/GLAH11.033/2003.02.21/contents.html>

I have looked at two files' DDS and DAS responses. Each file has 130
variables, although three are 'FakeDim' arrays. These FakeDim arrays are
pseudo Maps for browse images (Byte arrays named BROWSE_Image_00 or
..._01 and are not used by any of the other variables. All of the other
variables are similar to the Swath data except that the independent
variables are one-dimensional arrays that index time only with a
frequency of 0.25, 1 or 40 Hz. The main independent variables (which
every dependent has one of) are:

    Float64 Data_1HZ_DS_UTCTime_1[Data_1HZ_DS_UTCTime_1 = 81216];
    Float64 Data_1HZ_Time_d_UTCTime_1[Data_1HZ_Time_d_UTCTime_1 = 81216];

    Float64 Data_40HZ_DS_UTCTime_40[Data_40HZ_DS_UTCTime_40 = 3248640];
    Float64 Data_40HZ_Time_d_UTCTime_40[Data_40HZ_Time_d_UTCTime_40 = 3248640];

    Float64 Data_4s_DS_UTCTime_4s[Data_4s_DS_UTCTime_4s = 20304];
    Float64 Data_4s_Time_d_UTCTime_4s[Data_4s_Time_d_UTCTime_4s = 20304];

Note that looking at the values of *Data_1HZ_DS_UTCTime_1* and
*Data_1HZ_Time_d_UTCTime_1* there's no difference. I don't know why
these appear doubled in the files, but it may be an artifact of the
handler transforming the data 'into CF'.

Here are the file's variables I think are 'independent variables':

        Float64 Data_1HZ_Time_d_UTCTime_1[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float64 Data_1HZ_DS_UTCTime_1[Data_1HZ_DS_UTCTime_1 = 81208];

        Float64 Data_1HZ_Geolocation_d_lat[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float64 Data_1HZ_Geolocation_d_lon[Data_1HZ_Time_d_UTCTime_1 = 81208];


        Float64 Data_40HZ_DS_UTCTime_40[Data_40HZ_DS_UTCTime_40 = 3248320];
        Float64 Data_40HZ_Time_d_UTCTime_40[Data_40HZ_Time_d_UTCTime_40 = 3248320];

        Float64 Data_40HZ_Geolocation_d_lat[Data_40HZ_Time_d_UTCTime_40 = 3248320];
        Float64 Data_40HZ_Geolocation_d_lon[Data_40HZ_Time_d_UTCTime_40 = 3248320];

        Float64 Data_4s_Time_d_UTCTime_4s[Data_4s_Time_d_UTCTime_4s = 20302];
        Float64 Data_4s_DS_UTCTime_4s[Data_4s_DS_UTCTime_4s = 20302];

        Float64 Data_4s_Geolocation_d_lat[Data_4s_Time_d_UTCTime_4s = 20302];
        Float64 Data_4s_Geolocation_d_lon[Data_4s_Time_d_UTCTime_4s = 20302];

Here are all of the variables that have only one dimension:

        Float32 Data_1HZ_Angle_r_beam_azimuth[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Angle_r_beam_coelev[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Angle_r_pad_angle[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Flags_surf_is_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Flags_surf_ld_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Flags_surf_oc_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Flags_surf_si_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_Surface_pres[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_Surface_relh[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_Surface_temp[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_Surface_wdir[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_Surface_wind[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Geophysical_r_cld1_grd_det[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_OD1064CloudLayers_i_MRCir_af[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_actual_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_gyro_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_ist_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_lrs_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_oceansw_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_offnadir_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_pointing_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_att_steering_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_i_LidarQF[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_array_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_att_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_gps_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_man_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_model_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_Quality_orbit_pred_flg[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_RangeDelay_i_blow_snow_conf[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int16 Data_1HZ_RangeDelay_i_cld1_mswf[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_bs_erd[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_erd[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_pse[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_rdu[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_reflCor_atm[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_reflct_1064msf_1hz[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_reflct_1064od_1hz_cor[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_RangeDelay_r_reflct_pristine_1hz[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Float32 Data_1HZ_Reflectivity_r_SolAng[Data_1HZ_Time_d_UTCTime_1 = 81208];
        Int32 Data_1HZ_Time_i_rec_ndx[Data_1HZ_Time_d_UTCTime_1 = 81208];

        Float32 Data_40HZ_OpticalDepth_r_reflct_1064msf_40hz[Data_40HZ_Time_d_UTCTime_40 = 3248320];
        Float32 Data_40HZ_OpticalDepth_r_reflct_1064od_40hz_cor[Data_40HZ_Time_d_UTCTime_40 = 3248320];
        Int32 Data_40HZ_Time_i_rec_ndx[Data_40HZ_Time_d_UTCTime_40 = 3248320];
        Int32 Data_40HZ_Time_i_shot_count[Data_40HZ_Time_d_UTCTime_40 = 3248320];

        Int16 Data_4s_LowResAerosol_OD_i_aod_flg_4s[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_LowResAerosol_OD_i_pbl4_qf[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_LowResAerosol_OD_i_pbl4_uf[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_LowResAerosol_OD_r_aod_4s[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_Aer_PBL_LR_grd_det[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_Aer_PBL_LR_pres[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_Aer_PBL_LR_relh[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_Aer_PBL_LR_temp[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_aer4_ht[Data_4s_Time_d_UTCTime_4s = 20302];
        Float32 Data_4s_PBL4_od_r_pbl4_od[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Flags_i_AttFlg3[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Time_ddelay_flg[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Time_gps_time_flg[Data_4s_Time_d_UTCTime_4s = 20302];
        Int32 Data_4s_Time_i_rec_ndx[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Time_peaktp_flg[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Time_pl_timing_flg[Data_4s_Time_d_UTCTime_4s = 20302];
        Int16 Data_4s_Time_shot_time_flg[Data_4s_Time_d_UTCTime_4s = 20302];

And here are the dependent variables with two independent
variables/dimensions:

        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldtop_pres[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_cld1_bot[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Int16 Data_1HZ_OD532CloudLayer_i_cld1_qf[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_cld1_top[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_cld1_msf[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Int16 Data_1HZ_OD532CloudLayer_i_cld1_uf[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldtop_temp[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_cld1_od[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldbot_pres[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldbot_relh[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldbot_temp[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD532CloudLayer_r_MRg_cldtop_relh[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldbot_pres[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldtop_relh[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_cld_ir_OD[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cld_bot[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldbot_relh[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldtop_pres[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Int16 Data_1HZ_OD1064CloudLayers_i_MRir_QAFlag[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldbot_temp[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cldtop_temp[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_1HZ_OD1064CloudLayers_r_MRir_cld_top[Data_1HZ_Time_d_UTCTime_1 = 81208][Data_1HZ_DS_Cloud_Layer_10 = 10];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_aod_ratio[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_top[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_8 = 8];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_msf[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_sval1[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Int16 Data_4s_LowResAerosol_OD_i_aer4_uf[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_8 = 8];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_bot_pres[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Int16 Data_4s_LowResAerosol_OD_i_aer4_sval_uf[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_top_temp[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_bot_relh[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_bot_temp[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_top_pres[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_bot[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_8 = 8];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_sval_ratio[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_LowResAerosol_OD_r_aer4_od[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_8 = 8];
        Int16 Data_4s_LowResAerosol_OD_i_aer4_qf[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_8 = 8];
        Float32 Data_4s_LowResAerosol_OD_r_Aer_top_relh[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_9 = 9];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_top[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_top_pres[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_top_temp[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_bot_relh[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_bot_pres[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_top_relh[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_OD[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_bot_temp[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];
        Float32 Data_4s_Aerosol1064_OD_r_Aer_ir_bot[Data_4s_Time_d_UTCTime_4s = 20302][Data_4s_DS_Cloud_Layer_2 = 2];

## Resources

<font size="-2" color="green">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

|                                             |                                                                                 |                                                                          |                                                                            |                                                                               |
|---------------------------------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| Resource                                    | Owner                                                                           | Description                                                              | Availability                                                               | Source System                                                                 |
| <font size="-2" color="green">*name*</font> | <font size="-2" color="green">*Organization that owns/ manages resource*</font> | <font size="-2" color="green">*Short description of the resource*</font> | <font size="-2" color="green">*How often the resource is available*</font> | <font size="-2" color="green">*Name of system which provides resource*</font> |