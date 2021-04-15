---
author: admin
comments: true
date: 2015-08-05 20:03:12+00:00
layout: post
link: https://roberthorvick.com/2015/08/05/mapping-transit-routes-with-firebasesharp-2/
slug: mapping-transit-routes-with-firebasesharp-2
title: Mapping Transit Routes with FirebaseSharp 2
wordpress_id: 512
categories:
- Firebase
- Programming
---

Firebase provides a few [open data sets](https://www.firebase.com/docs/open-data/), one of which is the [Transit](https://publicdata-transit.firebaseio.com/) data set.  This data set includes real-time information about municipal transit systems - for example the [San Francisco Municipal](https://publicdata-transit.firebaseio.com/sf-muni) transit system.

I wanted to create a simple application that would watch the SF Muni system and display the location of buses in real-time.  I decided to start with the [GMap.NET](https://greatmaps.codeplex.com/) WPF library for mapping - it's pretty dead simple.

The whole thing is about 40 lines of code.  Here's the form load event handler:


    
    
    private void MainWindow_OnLoaded(object sender, RoutedEventArgs e)
    {
        MainMap.MapProvider = GMapProviders.OpenStreetMap;
    
        _app = new FirebaseApp(new Uri("https://publicdata-transit.firebaseio.com/sf-muni/vehicles"));
        var vehicles = _app.Child("/");
    
        vehicles.On("child_changed", (snap, child, context) =>
        {
            UpdateMarker(snap);
        });
    
        vehicles.On("child_removed", (snap, child, context) =>
        {
            RemoveMarker(snap);
        });
    }
    



Notice that we're setting up to use the OpenStreetMap provider (easiest option) and created the FirebaseApp instance at the "sf-muni/vehicles" root (since I don't care about routes or other locations, this is minimize the amount of data we need to cache).

Then I subscribe to the child changed and removed events calling the appropriate method to update or remove the marker (passing the snapshot along).  That's all the Firebase setup that was needed.


    
    
    private void UpdateMarker(IDataSnapshot snap)
    {
        Dispatcher.Invoke(() =>
        {
            PointLatLng position = new PointLatLng(snap["lat"].Value<double>(), snap["lon"].Value<double>());
    
            var marker = MainMap.Markers.FirstOrDefault(m => m.Tag.ToString() == snap.Key);
            if (marker == null)
            {
                marker = new GMapMarker(position)
                {
                    Tag = snap.Key,
                    Shape = CreateMarker(snap),
                };
    
                MainMap.Markers.Add(marker);
                MainMap.ZoomAndCenterMarkers(null);
            }
            else
            {
                marker.Position = position;
                ((Rectangle) marker.Shape).Fill = SpeedBrush(snap["speedKmHr"].Value<int>());
            }
        });
    }
    



In UpdateMarker I simply check to see if we already have the marker for this bus (based on the snapshot key) - and if we don't I create it.  Otherwise I update the position.  Since the Markers collection is an observable collection this is all it takes to update the marker locations (and colors - green means moving, red means stopped).


    
    
    private void RemoveMarker(IDataSnapshot snap)
    {
        Dispatcher.Invoke(() =>
        {
            var marker = MainMap.Markers.FirstOrDefault(m => m.Tag.ToString() == snap.Key);
            if (marker != null)
            {
                MainMap.Markers.Remove(marker);
                MainMap.ZoomAndCenterMarkers(null);
            }
        });
    }
    



RemoveMarker simply finds the marker and removes it if it exists.  Buses come and go more often than you'd think (any time they cross one of the bridges they are coming or going).

The rest is just creating a rect for the marker and adding a click handler as an example of how to do that.


    
    
    Brush SpeedBrush(int speed)
    {
        return speed > 0
            ? Brushes.Green
            : Brushes.Red;
    }
    
    private UIElement CreateMarker(IDataSnapshot snap)
    {
        Rectangle element = new Rectangle
        {
            Height = 10,
            Width = 10,
            Fill = SpeedBrush(snap["speedKmHr"].Value<int>()),
        };
    
        element.MouseLeftButtonDown += (sender, args) =>
        {
            MessageBox.Show(string.Format("Bus: {0}", snap.Key));
        };
    
        return element;
    }
    



_Note: the video is speed up by 2500% - buses only move that fast when [Sandra Bullock](http://www.imdb.com/title/tt0111257/) is behind the wheel_
