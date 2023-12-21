```tsx
import formidable from "formidable";

import FormData from "form-data";

import fs from "fs";

import { NextApiRequest, NextApiResponse } from "next";

import axios from "axios";

import { getSession } from "next-auth/react";

import { getIronSession } from "iron-session";

  

export const config = {

  api: {

    bodyParser: false,

  },

};

  

export default async function handler(

  req: NextApiRequest,

  res: NextApiResponse

) {
// 만약, /api/file/enterprise/modifyEnterpriseInfo로 post요청 시
  const { method, query } = req;
  const { slug } = req.query;
// slug: [enterprise, modifyEnterpriseInfo]

  

  if (slug === undefined) {

    return res

      .status(404)

      .json({ code: 404, data: null, msg: "요청한 주소가 존재하지 않습니다" });

  }

  

  const url = createReqUrl(slug);
// url is: /bapi/enterprise/modifyEnterpriseInfo
  console.log("url is ", url);

  

  const headers = await getHeaders(req, res);

  

  if (method === "POST") {

    let postHeaders: any = { ...headers };

  

    config.api.bodyParser = false;

    const fileData: formidable.Files = await new Promise((resolve, reject) => {

      const form = new formidable.IncomingForm({

        keepExtensions: true,

        multiples: true,

      });

  

      form.parse(req, (err, fields, files) => {

        if (err) return reject(err);

        // @ts-ignore

        return resolve({ fields, files });

      });

    });

  

    const formData = new FormData();

  

    let files = fileData.files;

    const fileKeys = Object.keys(files);

  

    if (fileKeys.length > 0) {

      for (let i = 0; i < fileKeys.length; i++) {

        const key = fileKeys[i];

        // @ts-ignore

        const files = fileData.files[key];

        if (files.length !== undefined) {

          for (let k = 0; k < files.length; k++) {

            const file = files[k];

            const readStream = fs.createReadStream(file.filepath);

            formData.append(key, readStream, file.originalFilename);

          }

        } else {

          const file = files;

          const readStream = fs.createReadStream(file.filepath);

          formData.append(key, readStream, file.originalFilename);

        }

      }

    }

  

    const fieldsKeys = Object.keys(fileData.fields);

  

    if (fieldsKeys.length > 0) {

      for (let i = 0; i < fieldsKeys.length; i++) {

        const key = fieldsKeys[i];

        // @ts-ignore

        const obj = fileData.fields[fieldsKeys[i]];

        formData.append(key, obj);

      }

    }

  

    try {

      postHeaders["Content-Type"] =

        "multipart/form-data; boundary=" + formData.getBoundary();

      const { data } = await axios.post(

        (process.env.NEXT_PUBLIC_HOST as string) + url,

        formData,

        {

          headers: postHeaders,

        }

      );

  

      if (data.status === "fail") {

        return res.redirect("/api/logout");

      }

      res.status(200).json(data);

    } catch (e: any) {

      console.log(e);

      if (e.response) {

        return res.status(e.response.status).json(e.response.data);

      } else if (e.request) {

      } else {

        console.log(e.message);

      }

      // return res.status(e.)

    }

  }

}

  

// async function getHeaders(req: NextApiRequest) {

//   const session = await getSession();

//

//   return {

//     "X-AUTH-TOKEN": session?.user.jwtAuthToken,

//     Cookie: "X-REFRESH_TOKEN=" + session?.user.jwtRefreshToken,

//     withCredentials: true,

//   };

// }

  

async function getHeaders(req: NextApiRequest, res: NextApiResponse) {

  const session = await getIronSession(req, res, {

    cookieName: "deps",

    password: process.env.NEXTAUTH_SECRET as string,

    cookieOptions: {

      secure: false, // process.env.NODE_ENV === "production",

    },

  });

  return {

    "X-AUTH-TOKEN": session.user?.jwtAuthToken,

    Cookie: "X-REFRESH_TOKEN=" + session.user?.jwtRefreshToken,

    withCredentials: true,

  };

}

  

function createReqUrl(slug: string | any[]) {

  let url = "/bapi";

  for (let i = 0; i < slug.length; i++) {

    url += "/";

    url += slug[i];

  }

  return url;

}
```