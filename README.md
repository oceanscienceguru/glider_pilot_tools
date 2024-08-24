
<!-- README.md is generated from README.Rmd. Please edit that file -->

# glider_pilot_tools

<!-- badges: start -->
<!-- badges: end -->

The goal of glider_pilot_tools is to provide glider pilots with
additional information about vehicle health and science data. As well,
it is a shareable platform built on R Shiny that can be used to keep
non-pilot collaborators informed of mission progress without having to
grant access to the piloting server.

Minimal sbdlist requirements for full features:

    c_wpt_lat              #at least once per surfacing interval
    c_wpt_lon              #at least once per surfacing interval
    m_present_time         #as frequent as possible
    m_coulomb_amphr_total  #at least once per surfacing interval
    m_gps_lat              #as frequently as possible (only writes with gps lock)
    m_gps_lon              #as frequently as possible (only writes with gps lock)
    m_leakdetect*          #as often as desired

Minimum tbdlist requirements:

    sci_m_present_time     #as frequent as possible
    sci_water_conductivity #as often as desired
    sci_water_temperature  #as often as desired
    sci_water_pressure     #as often as desired

Able to process intermediate and full data \*bd and \*cd files.

Requires [osgUtils](https://github.com/oceanscienceguru/osgUtils) R
package to function.
