
<!-- README.md is generated from README.Rmd. Please edit that file -->

# shiny_gliders

<!-- badges: start -->
<!-- badges: end -->

This tool started as a means to view full datasets for Slocum glider
vehicle troubleshooting and performance analysis. After creating a UI
and handful of standardized means of plotting full missions, we extended
the same framework to view and explore real-time decimated datasets as
they come in from gliders currently in the water. Requires
[osgUtils](https://github.com/oceanscienceguru/osgUtils) R package to
function.

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

Able to process intermediate and full data \*bd and \*cd files after
conversion into ASCII format using standard `dbd2asc` tool from
Teledyne.

# Setup

The original deployment of this app was on an Intel NUC running [Shiny
Server](https://posit.co/products/open-source/shiny-server/ "Link to Posit's Shiny Server page").
This machine is on the same network as the primary Slocum glider
dockserver. A cron job (see gliderCopy.sh file for example) synchronizes
the from-glider and archive folders from the primary server to the
remote server for processing for the app using rsync. Each
synchronization uses the default `dbd2asc` program for Linux from
Teledyne to convert the binary glider data into ASCII format as a
standard .ssv. The outputted .ssv files need to be arranged in a
specific folder structure for the R conversion cron job to function as
expected (see below). Each data type (flight/science) needs to end up in
its respective folder (`/echos` is the top level in this example).

    -- echos
       |__gliders
          |__usf-bull
             |__flight
             |__science

Next, the R conversion script (gliderChron.R) requires a similar,
separate folder structure (see below). Within each glider’s folder, two
.csv files are maintained and a .RData containing all of the objects
used for live data display.

    -- echos
       |__processed-gliders
          |__usf-bull

This script reads a controlling file (“processGliders.txt”) that
contains a list of glider names and battery capacities that informs
which glider data should be processed. Glider names can be commented out
when not in use. Similarly, the “deployedGliders.txt” file controls
which gliders are actively displayed on the dashboard and follows the
same structure. In the example below, usf-bass is a G1 glider and does
not have a coulomb amphr counter board, so capacity is marked as 000 and
none of the amphr-specific calculations will be displayed. As well,
usf-jaialai is commented out and will not be processed on the cron job.

    #usf-jaialai 550
    usf-bass     000
    usf-gansett  215

Additionally, one may store example goto_l\*.ma files on the server in a
folder named “routes” under the top level directory. These can be used
for quick checks or route planning using the routing module.

Similarly, a standard naming convention for full datasets is used to
parse mission information. Example: M139_usf-bull.RData parses out the
mission name/number “M139” and the glider name “usf-bull”. The source
data for the full data import function is a .ssv of the entire mission
containing both flight and science data after it has been merged into a
single .ssv.

# Daily email

Generated using the batteryMarkdown.Rmd script in Documentation. A
dedicated cron job runs this markdown script via
batteryMarkdownGenerate.R for each glider listed under the controlling
file called “processGliders.txt”. This is then saved as an html file and
emailed out daily using a separate cron job to a given list of emails.
