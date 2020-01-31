library(tidyverse)
library(plotly)
library(ggplot2)
library(naniar)
library(leaflet)
library(htmltools)

setwd("~/Desktop/Winter 2020/Data 598C Data Science Process/Data Science Project")

data1 <- read.csv(file = "data/listings.csv", header = TRUE)
data2 <- read.csv(file = "data/calendar.csv")
data3 <- read.csv(file = "data/reviews.csv")

data_train <- data1
#data_train <- rbind(data1, data2, data3)

#viewing data 
View(data_train)

#to view data kinda nicely - uses dplyr
glimpse(data_train)
str(data1)
summary(data_train)
head(data_train, 1000)
tail(data_train, 50)

#deleting some columns 
#cols_to_delete <- c("last_scraped", "thumbnail_url", "host_picture_url", "medium_url", "picture_url", "xl_picture_url", "host_thumbnail_url", "host_picture_url")
#select(data_train, -contains("url"))
data_train$last_scraped <- NULL
data_train$thumbnail_url <- NULL
data_train$host_picture_url <- NULL
data_train$medium_url <- NULL
data_train$picture_url <- NULL
data_train$xl_picture_url <- NULL
data_train$host_thumbnail_url <- NULL
data_train$host_picture_url <- NULL
data_train$host_url <- NULL
data_train$scrape_id <- NULL
data_train$experiences_offered <- NULL
data_train$neighborhood_overview <- NULL
data_train$host_about <- NULL
data_train$host_id <- NULL
data_train$host_verifications <- NULL
data_train$host_has_profile_pic <- NULL
data_train$host_identity_verified <- NULL
data_train$calendar_last_scraped <- NULL
data_train$review_scores_communication <- NULL
data_train$review_scores_location <- NULL
data_train$review_scores_value <- NULL
data_train$license <- NULL
data_train$instant_bookable <- NULL
data_train$require_guest_profile_picture <- NULL
data_train$require_guest_phone_verification <- NULL
data_train$summary <- NULL
data_train$notes <- NULL
#Down to 66 columns from initial 92

rownames(data_train) <- NULL

glimpse(data_train)
summary(data_train$is_location_exact)

#getting summary of all fields
summary_df <- data.frame(summary(data_train))
write.csv(summary_df, "summary.csv")
dim(data_train)[17]

#Looking for missing values
check_missing<- function(x){
  if (is.character(x)) sum(x=="") else sum(is.na(x))
}
NMISS<-data.frame(nmiss=sapply(data_train,check_missing))
write.csv(NMISS, "missing.csv")

#plotting missing data
missingdata <- data_train
missingdata[missingdata == ""] <- NA
missingdata <- missingdata %>% select(host_is_superhost, review_scores_rating,
                                      host_response_time, name, host_since, zipcode, latitude,longitude,
                                      host_location, host_response_rate,neighbourhood_cleansed, country, country_code,
                                      property_type, price,weekly_price, monthly_price)
gg_miss_var(missingdata) + labs(title = "Missing Values Plot",y = "Missing Data", x = "Attribute")  + theme_bw() + theme(text=element_text(color = "dark blue"))

#In neighborhood group cleansed, there are 794 rows with "Other Neighborhoods"
#Need to fix this - maybe look at neighorhood cleansed column instead?
#Looking at categorical data now
sort(table(data_train$host_neighbourhood), decreasing = TRUE)
head(subset(data_train, select = 'neighbourhood_group_cleansed'))
factor(data_train$neighbourhood_group_cleansed)
count(data_train, 'neighbourhood_group_cleansed')
count(data_train, 'neighbourhood_cleansed')

for(i in data_train)
  if(i[neighbourhood_group_cleansed] == "Other neighborhoods")
    i[neighbourhood_group_cleansed] <-  i[neighborhood_cleansed]

#Plotting count of listings per neighborhood
neigborhood_data <- table(data_train$neighbourhood_cleansed)
barplot(sort(table(data_train$neighbourhood_group_cleansed), decreasing = TRUE), ylab = "Count",horiz = FALSE , main = "Distribution of Listings Per Neighborhood", beside = FALSE)
barplot(table(data_train$neighbourhood_group_cleansed), density = 20, ylab = "Count",horiz = FALSE , main = "Distribution of Listings Per Neighborhood", beside = FALSE)

#ggplot(as.data.frame(table(data_train$neighbourhood_cleansed)), aes(factor(neighborhood), Freq, fill = Freq)) + xlim(0,10) + geom_col(position = 'dodge')

write.csv(neigborhood_data, "neighborhoodcount.csv")
count(data_train$neighbourhood_cleansed)
plot(data_train$neighbourhood_cleansed, xlab = 'Neighborhoods', ylab = 'Count')
neighborhood <- (data_train$neighbourhood_cleansed)
#x <- sort(count((neighborhood)))
#arrange(data_train, neighbourhood_cleansed)
neighborhood_data <- data_train[order(data_train[,21] ),]
summary(neighborhood_data$neighbourhood_cleansed)

#
hist(data_train$host_listings_count, main = "Count of Host Listings", xlab = "", ylab = "Count")
barchart(data_train$neighbourhood_cleansed, main = "Neighborhood Distribution", xlab = "")
barchart(data_train$property_type, main = "Typess of Airbnb Properties", xlab = "Count")
plot(data_train$host_is_superhost)

#plotting lat long on a map

#Reading Listings Data
listingdf <- read.csv('./listings.csv')
#Creating a link for each pop up

#Creating Listings across Seattle
leaflet(listingdf) %>%
  addTiles() %>%
  addMarkers(~longitude, ~latitude,labelOptions = labelOptions(noHide = T),
             clusterOptions = markerClusterOptions(),
             popup = paste0("<b> Name: </b>", listingdf$name , "<br/><b> Host Name: </b>", 
                            listingdf$host_name, "<br> <b> Price/night: </b>", listingdf$price, "<br/><b> Room Type: </b>", 
                            listingdf$room_type, "<br/><b> Property Type: </b>", listingdf$property_type
             )) %>% 
  setView(-122.335167, 47.608013, zoom = 11) %>%
  addProviderTiles("CartoDB.Positron")

#This shows listings in a clustered fashion. Clicking on a cluster zooms into the listings belonging to it.
#Clicking on a pin gives you a pop up with some details about the property.
#We can see that most of the listings are clustered around Downtown and Cap Hill.
