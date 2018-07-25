##Shiny + shinydashboard + googleVis = Powerful Interactive Visiualization

If you are a data scientist, who spent several weeks on developing a fantanstic model, you'd like to have equally awesome way to visualize and demo yours results. For R users, ggplots are no longer sufficient. R-shiny + shinydashboard + googleVis could be a wonderful combination for a quick demo application.

### R Shiny
R Shiny is interactive tool for R user to seemlessly integrate with R and quickly develop a demo app.
key concepts:
UI: a web page connected with backend R session. It's basically written using HTML/Javascript/CSS. By default, Shiny use the `fluidPage` template, so it is written as `ui <- fluidPage()`.
Server: the backend R session, which does all the data manipulation and calculation. The model prediction part should sit here. The server can be a function of `input`, `output` and `session`, so it is written as `server <- function(input, output, session){}`. `input` is a list of variables pass from UI, `output` is a list of varialbes rendered from Server and ready to display in UI.
App: a shiny app, comprises `ui` and `server`. It can be two seperate file named `ui.R` and `server.R`, or a single file named `app.R`, consisting ui function, server function and `shinyApp(ui, server)`

reference: https://shiny.rstudio.com/tutorial/

### shinydashboard package
shinydashboard provide neat and nice interface, with customizable header, sidebar and body. I usually just need to define my title in header, list the tabs or selection panel in sidebar, and at last assemble all the tables and charts into the body section. Make use of `fluidRow` and `column` function to align your components in body. 
A glimpse of shinydashboard template:
![https://rstudio.github.io/shinydashboard/images/dashboard.png]
reference: https://rstudio.github.io/shinydashboard/get_started.html

### googleVis for R
Google Vis is developed using Javascript and R package is available as `googleVis` in CRAN, with limited flexibility. Simple bar-chart, line-chart, tables are easily construsted and be able to meet our general needs. Here is the exmample plots using googleVis: https://cran.r-project.org/web/packages/googleVis/vignettes/googleVis_examples.html
Detail customerization of the charts need look up the documentation of google's javascript API and supply the setting as JSON format text to the `options` in R (e.g. `options = list(hAxis = "{textStyle:{fontSize:12},format:'percent',minValue:0,maxValue:1}"`)  
Google Vis Official Documenation: https://cran.r-project.org/web/packages/googleVis/vignettes/googleVis_examples.html

### leaflet for R
googleVis is good enough for most of charts, except for maps, because maps in googleVis is replied on googleMap API, which is not free of charge. Leaflet for R is an good alternative.
reference: https://rstudio.github.io/leaflet/

