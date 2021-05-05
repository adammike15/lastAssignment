using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

using Amazon.Lambda.Core;
using System.Net.Http;
using Newtonsoft.Json;
using Amazon.Lambda.APIGatewayEvents;
using System.Dynamic;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.DocumentModel;
using Amazon.DynamoDBv2.Model;

// Assembly attribute to enable the Lambda function's JSON input to be converted into a .NET class.
[assembly: LambdaSerializer(typeof(Amazon.Lambda.Serialization.SystemTextJson.DefaultLambdaJsonSerializer))]

namespace govSpendingDataConnect
{
    //create a class for incoming objects to be created
    /*
    public class StateListing
    {
        public string name;
        public string code;
        public string fips;
        public double amount;
        public string type;
    }
    */

    public class Function
    {
        //create a client to facilitate HTTP requests
        HttpClient client = new HttpClient();

        //create new connection to dynamo db
        private static AmazonDynamoDBClient dynamoClient = new AmazonDynamoDBClient();
        private string tableName = "spending";



        public async Task<ExpandoObject> FunctionHandler(String input, ILambdaContext context)
        {


            //gather uRL to connect to
            var url = "https://api.usaspending.gov/api/v2/recipient/state/";
            //var url = "https://official-joke-api.appspot.com/random_ten";

            //Query the url
            string response = await client.GetStringAsync(url);



            //convert results into objects
            dynamic expando = JsonConvert.DeserializeObject<ExpandoObject>(response);
            //StateListing slisting = JsonConvert.DeserializeObject<StateListing>(response);
             

            //place items from list into dynamo db table
            //Table govSpending = Table.LoadTable(dynamoClient, tableName);

            //-AWS Convention below.  If it's not present, document will return NULL below
            //-----------------------------------------------
            PutItemOperationConfig config = new PutItemOperationConfig();
            config.ReturnValues = ReturnValues.AllOldAttributes;


            return expando;
        }
    }
}
