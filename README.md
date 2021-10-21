- 👋 Hi, I’m @secginn
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
secginn/secginn is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
{
  "openapi": "3.0.1",
  "info": {
    "title": "Coronawarn.app Quick-test Proxy",
    "description": "The API defines how 3rd parties can insert results of quick-tests into the Coronawarn.app infrastructure. Be aware, that the communication with the quick-test proxy is secured by a mTLS connection",
    "license": {
      "name": "Apache 2.0",
      "url": "http://www.apache.org/licenses/LICENSE-2.0"
    },
    "version": "1.2.1-SNAPSHOT"
  },
  "servers": [
    {
      "url": "https://quicktest-result.coronawarn.app",
      "variables": {},
      "description": "quicktest-proxy PRODUCTION stage"
    },
    {
      "url": "https://quicktest-result-cff4f7147260.coronawarn.app",
      "variables": {},
      "description": "quicktest-proxy WRU (Wirk-Referenz-Umgebung) stage, Coronawarn.app goes here"
    }
  ],
  "paths": {
    "/api/v1/quicktest/result": {
      "post": {
        "tags": [
          "test-result-controller"
        ],
        "summary": "Request the test result for a Rapid Antigen Test by its ID.",
        "description": "For a given Test ID the result of a RAT can be requested. Please note: you need to hash (sha256) the Test ID ones more and send it to the server in this request.",
        "operationId": "quickTestResult",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/TestResultRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "OK. Testresult visible server Response.",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TestResultResponse"
                }
              }
            }
          }
        }
      }
    },
    "/api/v1/quicktest/results": {
      "post": {
        "tags": [
          "test-result-controller"
        ],
        "summary": "Set multiple testresults for a Rapid Antigen Test as an array.",
        "description": "The result and the sc (sample collection) timestamp of a RAT can be set.",
        "operationId": "quicktestResults",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/QuickTestResultList"
              }
            }
          },
          "required": true
        },
        "responses": {
          "204": {
            "description": "No content, RAT result(s) inserted successfully.",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object"
                }
              }
            }
          }
        }
      }
    }
  },
  "components": {
    "schemas": {
      "TestResultRequest": {
        "required": [
          "id"
        ],
        "type": "object",
        "properties": {
          "id": {
            "pattern": "^[XxA-Fa-f0-9]([A-Fa-f0-9]{63})$",
			"description": "SHA-256 Hash of the identification attribute (double sha256 hashing)",
            "type": "string"
          }
        },
        "description": "The test result request model."
      },
      "TestResultResponse": {
        "required": [
          "testResult"
        ],
        "type": "object",
        "properties": {
          "testResult": {
            "maximum": 8,
            "minimum": 5,
            "type": "integer",
            "format": "int32",
			"description": "testresult for the given test: 5=pending, 6=negative, 7=positive, 8=test invalid"
          },
        },
        "description": "The test result response model."
      },
      "QuickTestResult": {
        "required": [
          "id",
          "result"
        ],
        "type": "object",
        "properties": {
          "id": {
            "pattern": "^[XxA-Fa-f0-9]([A-Fa-f0-9]{63})$",
            "type": "string",
            "description": "the testId (Hashed GUID"
          },
          "result": {
            "maximum": 9,
            "minimum": 5,
            "type": "integer",
            "description": "the result of the rapid antigen test",
            "format": "int32"
          },
          "sc": {
            "type": "integer",
            "description": "the timestamp of the sample collection (sc) in unix epoch format. If not set, the time of insertion will be used instead",
            "format": "int64"
          }
        },
        "description": "The rapid antigen test result model."
      },
      "QuickTestResultList": {
        "required": [
          "testResults"
        ],
        "type": "object",
        "properties": {
          "testResults": {
            "type": "array",
            "description": "array of rapid antingen test results",
            "items": {
              "$ref": "#/components/schemas/QuickTestResult"
            }
          },
        },
        "description": "The rapid antigen test result list model."
      }
    }
  }
}
