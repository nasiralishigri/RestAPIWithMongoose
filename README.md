# RestAPIWithMongoose

# Exercise (Instructions): REST API with Express, MongoDB and Mongoose Part 1
Objectives and Outcomes

# In this exercise, you will integrate the REST API server based on the Express framework that you implemented earlier, together with the Mongoose schema and models to create a full-fledged REST API server. At the end of this exercise, you will be able to:
 Develop a full-fledged REST API server with Express, MongoDB and Mongoose
 Serve up various REST API end points together with interaction with the MongoDB server.

Exercise Resources

# Update the Express Application
 Go to the conFusionServer folder where you had developed the REST API server using Express generator.
 Copy the models folder from the node-mongoose folder to the conFusionServer folder.
 Then install bluebird, mongoose and mongoose-currency Node modules by typing the following at the prompt:
              npm install mongoose@5.1.7 mongoose-currency@0.2.0 --save
#     Open app.js file and add in the code to connect to the MongoDB server as follows:
                  

            const mongoose = require('mongoose');

            const Dishes = require('./models/dishes');

            const url = 'mongodb://localhost:27017/conFusion';
            const connect = mongoose.connect(url);

            connect.then((db) => {
                console.log("Connected correctly to server");
            }, (err) => { console.log(err); });

           
  #      Next open dishes.js in the models folder and update it as follows:
         

              require('mongoose-currency').loadType(mongoose);
              const Currency = mongoose.Types.Currency;

              const dishSchema = new Schema({
                  name: {
                      type: String,
                      required: true,
                      unique: true
                  },
                  description: {
                      type: String,
                      required: true
                  },
                  image: {
                      type: String,
                      required: true
                  },
                  category: {
                      type: String,
                      required: true
                  },
                  label: {
                      type: String,
                      default: ''
                  },
                  price: {
                      type: Currency,
                      required: true,
                      min: 0
                  },
                  featured: {
                      type: Boolean,
                      default:false      
                  },
                  comments:[commentSchema]
              }, {
                  timestamps: true
              });
#      Now open dishRouter.js and update its code as follows:
       
                       const express = require('express');
                const bodyParser = require('body-parser');
                const mongoose = require('mongoose');

                const Dishes = require('../models/dishes');

                const dishRouter = express.Router();

                dishRouter.use(bodyParser.json());

                dishRouter.route('/')
                .get((req,res,next) => {
                    Dishes.find({})
                    .then((dishes) => {
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(dishes);
                    }, (err) => next(err))
                    .catch((err) => next(err));
                })
                .post((req, res, next) => {
                    Dishes.create(req.body)
                    .then((dish) => {
                        console.log('Dish Created ', dish);
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(dish);
                    }, (err) => next(err))
                    .catch((err) => next(err));
                })
                .put((req, res, next) => {
                    res.statusCode = 403;
                    res.end('PUT operation not supported on /dishes');
                })
                .delete((req, res, next) => {
                    Dishes.remove({})
                    .then((resp) => {
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(resp);
                    }, (err) => next(err))
                    .catch((err) => next(err));    
                });

                dishRouter.route('/:dishId')
                .get((req,res,next) => {
                    Dishes.findById(req.params.dishId)
                    .then((dish) => {
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(dish);
                    }, (err) => next(err))
                    .catch((err) => next(err));
                })
                .post((req, res, next) => {
                    res.statusCode = 403;
                    res.end('POST operation not supported on /dishes/'+ req.params.dishId);
                })
                .put((req, res, next) => {
                    Dishes.findByIdAndUpdate(req.params.dishId, {
                        $set: req.body
                    }, { new: true })
                    .then((dish) => {
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(dish);
                    }, (err) => next(err))
                    .catch((err) => next(err));
                })
                .delete((req, res, next) => {
                    Dishes.findByIdAndRemove(req.params.dishId)
                    .then((resp) => {
                        res.statusCode = 200;
                        res.setHeader('Content-Type', 'application/json');
                        res.json(resp);
                    }, (err) => next(err))
                    .catch((err) => next(err));
                });

                module.exports = dishRouter;

 Save the changes and start the server. Make sure your MongoDB server is up and running.
 You can now fire up postman and then perform several operations on the REST API. You can use the data for all the dishes provided in the db.json file given above in the Exercise Resources to test your server
#  db.json File for Test
                         {
                  "dishes": [
                    {
                      "name": "Uthappizza",
                      "image": "images/uthappizza.png",
                      "category": "mains",
                      "label": "Hot",
                      "price": "4.99",
                      "featured": "true",
                      "description": "A unique combination of Indian Uthappam (pancake) and Italian pizza, topped with Cerignola olives, ripe vine cherry tomatoes, Vidalia onion, Guntur chillies and Buffalo Paneer.",
                      "comments": [
                        {
                          "rating": 5,
                          "comment": "Imagine all the eatables, living in conFusion!",
                          "author": "John Lemon",
                          "date": "2012-10-16T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Sends anyone to heaven, I wish I could get my mother-in-law to eat it!",
                          "author": "Paul McVites",
                          "date": "2014-09-05T17:57:28.556094Z"
                        },
                        {
                          "rating": 3,
                          "comment": "Eat it, just eat it!",
                          "author": "Michael Jaikishan",
                          "date": "2015-02-13T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Ultimate, Reaching for the stars!",
                          "author": "Ringo Starry",
                          "date": "2013-12-02T17:57:28.556094Z"
                        },
                        {
                          "rating": 2,
                          "comment": "It's your birthday, we're gonna party!",
                          "author": "25 Cent",
                          "date": "2011-12-02T17:57:28.556094Z"
                        }
                      ]
                    },
                    {
                      "name": "Zucchipakoda",
                      "image": "images/zucchipakoda.png",
                      "category": "appetizer",
                      "label": "",
                      "price": "1.99",
                      "featured": "false",
                      "description": "Deep fried Zucchini coated with mildly spiced Chickpea flour batter accompanied with a sweet-tangy tamarind sauce",
                      "comments": [
                        {
                          "rating": 5,
                          "comment": "Imagine all the eatables, living in conFusion!",
                          "author": "John Lemon",
                          "date": "2012-10-16T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Sends anyone to heaven, I wish I could get my mother-in-law to eat it!",
                          "author": "Paul McVites",
                          "date": "2014-09-05T17:57:28.556094Z"
                        },
                        {
                          "rating": 3,
                          "comment": "Eat it, just eat it!",
                          "author": "Michael Jaikishan",
                          "date": "2015-02-13T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Ultimate, Reaching for the stars!",
                          "author": "Ringo Starry",
                          "date": "2013-12-02T17:57:28.556094Z"
                        },
                        {
                          "rating": 2,
                          "comment": "It's your birthday, we're gonna party!",
                          "author": "25 Cent",
                          "date": "2011-12-02T17:57:28.556094Z"
                        }
                      ]
                    },
                    {
                      "name": "Vadonut",
                      "image": "images/vadonut.png",
                      "category": "appetizer",
                      "label": "New",
                      "price": "1.99",
                      "featured": "false",
                      "description": "A quintessential ConFusion experience, is it a vada or is it a donut?",
                      "comments": [
                        {
                          "rating": 5,
                          "comment": "Imagine all the eatables, living in conFusion!",
                          "author": "John Lemon",
                          "date": "2012-10-16T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Sends anyone to heaven, I wish I could get my mother-in-law to eat it!",
                          "author": "Paul McVites",
                          "date": "2014-09-05T17:57:28.556094Z"
                        },
                        {
                          "rating": 3,
                          "comment": "Eat it, just eat it!",
                          "author": "Michael Jaikishan",
                          "date": "2015-02-13T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Ultimate, Reaching for the stars!",
                          "author": "Ringo Starry",
                          "date": "2013-12-02T17:57:28.556094Z"
                        },
                        {
                          "rating": 2,
                          "comment": "It's your birthday, we're gonna party!",
                          "author": "25 Cent",
                          "date": "2011-12-02T17:57:28.556094Z"
                        }
                      ]
                    },
                    {
                      "name": "ElaiCheese Cake",
                      "image": "images/elaicheesecake.png",
                      "category": "dessert",
                      "label": "",
                      "price": "2.99",
                      "featured": "false",
                      "description": "A delectable, semi-sweet New York Style Cheese Cake, with Graham cracker crust and spiced with Indian cardamoms",
                      "comments": [
                        {
                          "rating": 5,
                          "comment": "Imagine all the eatables, living in conFusion!",
                          "author": "John Lemon",
                          "date": "2012-10-16T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Sends anyone to heaven, I wish I could get my mother-in-law to eat it!",
                          "author": "Paul McVites",
                          "date": "2014-09-05T17:57:28.556094Z"
                        },
                        {
                          "rating": 3,
                          "comment": "Eat it, just eat it!",
                          "author": "Michael Jaikishan",
                          "date": "2015-02-13T17:57:28.556094Z"
                        },
                        {
                          "rating": 4,
                          "comment": "Ultimate, Reaching for the stars!",
                          "author": "Ringo Starry",
                          "date": "2013-12-02T17:57:28.556094Z"
                        },
                        {
                          "rating": 2,
                          "comment": "It's your birthday, we're gonna party!",
                          "author": "25 Cent",
                          "date": "2011-12-02T17:57:28.556094Z"
                        }
                      ]
                    }
                  ],
                  "promotions": [
                    {
                      "name": "Weekend Grand Buffet",
                      "image": "images/buffet.png",
                      "label": "New",
                      "price": "19.99",
                      "featured": "true",
                      "description": "Featuring mouthwatering combinations with a choice of five different salads, six enticing appetizers, six main entrees and five choicest desserts. Free flowing bubbly and soft drinks. All for just $19.99 per person "
                    }
                  ],
                  "leaders": [
                    {
                      "name": "Peter Pan",
                      "image": "images/alberto.png",
                      "designation": "Chief Epicurious Officer",
                      "abbr": "CEO",
                      "featured": "false",
                      "description": "Our CEO, Peter, credits his hardworking East Asian immigrant parents who undertook the arduous journey to the shores of America with the intention of giving their children the best future. His mother's wizardy in the kitchen whipping up the tastiest dishes with whatever is available inexpensively at the supermarket, was his first inspiration to create the fusion cuisines for which The Frying Pan became well known. He brings his zeal for fusion cuisines to this restaurant, pioneering cross-cultural culinary connections."
                    },
                    {
                      "name": "Dhanasekaran Witherspoon",
                      "image": "images/alberto.png",
                      "designation": "Chief Food Officer",
                      "abbr": "CFO",
                      "featured": "false",
                      "description": "Our CFO, Danny, as he is affectionately referred to by his colleagues, comes from a long established family tradition in farming and produce. His experiences growing up on a farm in the Australian outback gave him great appreciation for varieties of food sources. As he puts it in his own words, Everything that runs, wins, and everything that stays, pays!"
                    },
                    {
                      "name": "Agumbe Tang",
                      "image": "images/alberto.png",
                      "designation": "Chief Taste Officer",
                      "abbr": "CTO",
                      "featured": "false",
                      "description": "Blessed with the most discerning gustatory sense, Agumbe, our CFO, personally ensures that every dish that we serve meets his exacting tastes. Our chefs dread the tongue lashing that ensues if their dish does not meet his exacting standards. He lives by his motto, You click only if you survive my lick."
                    },
                    {
                      "name": "Alberto Somayya",
                      "image": "images/alberto.png",
                      "designation": "Executive Chef",
                      "abbr": "EC",
                      "featured": "true",
                      "description": "Award winning three-star Michelin chef with wide International experience having worked closely with whos-who in the culinary world, he specializes in creating mouthwatering Indo-Italian fusion experiences. He says, Put together the cuisines from the two craziest cultures, and you get a winning hit! Amma Mia!"
                    }
                  ],
                  "feedback": []
                }

