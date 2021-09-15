# hello-world
Just another repository
const cors = require('cors');
const rateLimit = require('express-rate-limit');
const {corsOptions} = require('./corsOptions');
const throttleSecs = Number(require('../config/app.config.js')
    .node.authentThrottleWindowSecs) || 30*60;
const throttleMax = Number(require('../config/app.config.js')
    .node.authentThrottleWindowMax) || 100;


const createAccountLimiter = rateLimit({
  windowMs: throttleSecs * 1000,
  max: throttleMax,
  message:
    {
      resultCode: '429000',
      developerMessage:
      // eslint-disable-next-line max-len
      `Too many accounts created from this IP, please try again ${throttleSecs} seconds`,
    },

});
module.exports = (app) => {
  const access = require('../controllers/Access.controller.js');
  const router = require('express').Router();
  router.route('/')
      .get(access.getToken);
  app.use('/callGw/v1/getToken',
      createAccountLimiter,
      cors(corsOptions), router);
};
