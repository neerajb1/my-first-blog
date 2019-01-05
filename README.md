<!DOCTYPE html>
<html>
 <head>
  <title> Select store</title>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.0/jquery.min.js"></script>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" />
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
 </head>
 <body>
      <link href="http://192.168.2.62:8077/static/app/scripts/nv.d3.css" rel="stylesheet" />
    <script src="http://192.168.2.62:8077/static/app/scripts/d3.min.js"></script>
    <script src="http://192.168.2.62:8077/static/app/scripts/nv.d3.js"></script>
<style>
   
.container
 {
  margin:auto;
  width:90%;
  padding:10px;
  border:outset;
 }
 select
{
  display:inline-block;
  cursor:pointer;
  
 }
    </style>

  <br /><br />
  <div id="dropDown" div class="container" style="width:600px;">
   <h2 align="center">Select store</h2><br /><br />
   <select name="country" id="country" class="form-control input" disp>
    <option value="">Select country</option>
      {% for country in country_name %}
      <option value="{{ country }}">{{ country }}</option>
    {% endfor %}
   </select>
   <br />
   <select name="city" id="city" class="form-control input">
    <option value="">Select city</option>
       {% for city in city_name %}
      <option value="{{ city }}">{{ city }}</option>
    {% endfor %}
   </select>
   <br />
   <select name="mall" id="mall" class="form-control input" >
    <option value="">Select Mall</option>
       {% for mall in mall_name %}
      <option value="{{ mall }}">{{ mall }}</option>
    {% endfor %}
   </select>
   <br />
      <select name="store" id="store" class="form-control input" >
    <option value="">Select store</option>
          {% for store in store_name %}
      <option value="{{ store }}">{{ store }}</option>
    {% endfor %}
   </select>
   <br />
     <button type="submit" class="btn btn-default bttn-primary" id="btnSearch" onclick="bindchart()">Search</button>
  </div>
  <div id="chart3" >
            <svg style="height: 50px;"></svg>
                     
    </div>
     
 </body>
</html>

<script>
    $(document).on('change', '#country', function(){
        var country_name = $(this).val();
        
        if(country_name != '')
        
            $.ajax({
                url: 'country/',
                data: {
                    'country_name': country_name ,
                   
                },
            
                success: function (response) {
                   
                    var html_code = '';
                    city_name = response["city_name"];
                    html_code += '<option value="">Select city' + '</option>';
                    $.each(city_name, function (i , value) {
                        html_code += '<option value="' + value + '">' + value + '</option>';
                    });

                    $('#city').html(html_code);

            }
        })
        
        else
        {
            $('#city').html('<option value="">Select city</option>');
            $('#mall').html('<option value="">Select mall</option>');
            $('#store').html('<option value="">Select store</option>');
        }
    });
    $(document).on('change', '#city', function () {
        var city_name = $(this).val();
        
        country = $('#country option:selected').val()
        if (city_name != '')

            $.ajax({
                url: 'city/',
                data: {
                    
                    'country_name': country,
                    'city_name': city_name,

                },

                success: function (response) {
                    
                    var html_code = '';
                    city_name = response["mall_name"];
                    html_code += '<option value="">Select mall' + '</option>';
                    $.each(city_name, function (i, value) {
                        html_code += '<option value="' + value + '">' + value + '</option>';
                    });

                    $('#mall').html(html_code);


                }
            })
        else {
            $('#mall').html('<option value="">Select mall</option>');
            $('#store').html('<option value="">Select store</option>');
        }
    });
    $(document).on('change', '#mall', function () {
        var mall_name = $(this).val();
        country = $('#country option:selected').val()
        city_name = $('#city option:selected').val()
        if (mall_name != '')
            $.ajax({
            url: 'mall/',
            data: {
                'country_name': country,
                'city_name': city_name,
                'mall_name': mall_name,

            },

            success: function (response) {
                
                var html_code = '';
                store_name = response["store_name"];
                
                html_code += '<option value="">Select store' + '</option>';
                $.each(store_name, function (i, value) {
                    html_code += '<option value="' + value + '">' + value + '</option>';
                });

                $('#store').html(html_code);
                

            }
        })
        else {
            $('#store').html('<option value="">Select store</option>');
        }
    });



    function bindchart() {

        if ($('#country').val() == "" && $('#city').val() == "" && $('#mall').val() == "" && $('#store').val() == "")
        { alert('Please select a value'); }

        else {
            country = $('#country option:selected').val()
            city = $('#city option:selected').val()
            mall = $('#mall option:selected').val()
            store = $('#store option:selected').val()
           

            $.ajax({
                url: 'report/',
                data: {
                    'country_name': country,
                    'city_name': city,
                    'mall_name': mall,
                    'store_name': store
                },
                success: function (response) {
                    country = $('#country option:selected').val()
                    city = $('#city option:selected').val()
                    mall = $('#mall option:selected').val()
                    store = $('#store option:selected').val()
                    //ur= "/report/?country_name=" + country + "&city_name=" + city +"&mall_name=" + mall + "&store_name=" + store
                    //alert(ur)
                    d3.json("/report/?country_name=" + country + "&city_name=" + city +"&mall_name=" + mall + "&store_name=" + store , function (data) {
                    
                        //console.log(data)
                    })
                    nv.addGraph(function () {
                        chart = nv.models.multiBarHorizontalChart()
                        .x(function (d) { return d[0] })
                    .y(function (d) { return d[1] }).margin({ bottom: 80 }).showControls(false)
                    .stacked(false);
                        if ((data[0]["values"]).length == 6 || (data[0]["values"]).length == 5 || (data[0]["values"]).length == 4) {
                            chart.groupSpacing(0.3);
                        }
                        else if ((data[0]["values"]).length == 3 || (data[0]["values"]).length == 2) {
                            chart.groupSpacing(0.5);
                        }
                        else if ((data[0]["values"]).length == 1) {
                            chart.groupSpacing(0.7);
                        }
                        else {

                            chart.groupSpacing(0.2);
                        }

                    });
                    //active_beacon_store = response["active_beacon_store"];

                    //console.log(active_beacon_store.length)
                    //non_active_beacon_store = response["non_active_beacon_store"];

                    //console.log(non_active_beacon_store.length)
                    //$.each(active_beacon_store, function (index, itemData) {
                    //    console.log(index)
                    //    $.each(this, function (k, v) {
                    //        console.log(k)
                    //        console.log(v)
                    //    });
                    //});

                    //$.each(non_active_beacon_store, function (index, itemData) {
                    //    console.log(index)
                    //    $.each(this, function (k, v) {
                    //        console.log(k)
                    //        console.log(v)
                    //    });
                    //});

                    }
                    
                    
                }
            );
        }



        window.onbeforeunload = function () {
            var Ans = confirm("Are you sure you want change page!");
            if (Ans == true)
                return true;
            else
                return false;
        };



    }


 


    function ShowLoader() {
        $("#loader").show();
        $("#btnSearch").prop('disabled', true);
    }

    function HideLoader() {
        $("#loader").hide();
        $("#btnSearch").prop('disabled', false);


    }
    HideLoader();
</script>
