# templates

## main

```ts
import type { components, operations } from './twitch-api.generated';

type Schema<T extends keyof components['schemas']> = components['schemas'][T];

%TYPE_EXPORTS%

type SuccessCode = 200 | 202 | 204;
type ErrorCode = 400 | 401 | 403 | 404 | 409 | 422 | 425 | 429 | 500;
export type ApiResponse<
  TData,
  TSuccessCode extends SuccessCode = SuccessCode,
  TErrorCode extends ErrorCode = ErrorCode,
> = Promise<
  | { ok: true; status: TSuccessCode; data: TData }
  | { ok: false; status: TErrorCode; data: unknown }
>;

const getSearchParams = <T extends Record<string, any>>(params: T) => {
  const kvPairs: string[] = [];
  for (const key of Object.keys(params)) {
    const value = params[key];
    if (Array.isArray(value)) {
      value.forEach((v) => kvPairs.push(`${key}=${v}`));
    } else {
      kvPairs.push(`${key}=${value}`);
    }
  }
  return kvPairs.join('&');
};

type CallApiOptions = {
  baseUrl?: string;
  path: string;
  method?: string;
  params?: any;
  body?: any;
  clientId?: string;
  accessToken?: string;
  requiresAuth?: boolean;
}

export type TwitchApiOptions = {
  accessToken?: string;
  clientId?: string;
};

export class TwitchApi {
  private _accessToken: string;
  private _clientId: string;

  constructor({ accessToken = '', clientId = '' }: TwitchApiOptions = {}) {
    this._accessToken = accessToken;
    this._clientId = clientId;
  }

  private async callApi({
    baseUrl = 'https://api.twitch.tv/helix',
    path,
    method = 'GET',
    params,
    body,
    clientId,
    accessToken,
    requiresAuth = true,
  }: CallApiOptions): Promise<any> {
    const url = params
      ? `${baseUrl}${path}?${getSearchParams(params)}`
      : `${baseUrl}${path}`;
    const options: RequestInit = { method };
    const headers = new Headers();
    options.headers = headers;
    if (body) {
      headers.set('Content-Type', 'application/json');
      options.body = JSON.stringify(body);
    }
    if (requiresAuth) {
      options.headers.set('Authorization', `Bearer ${accessToken || this._accessToken}`);
      options.headers.set('Client-Id', clientId || this._clientId);
    }
    const response = await fetch(url, options);
    return {
      ok: response.ok,
      status: response.status as any,
      data: await response.json(),
    };
  }

  %CONTENT%
}
```

## method-comment

```md
%DESCRIPTION%

__URL:__

`%METHOD% %URL%`

%RESPONSE_CODES%

@see %DOCS_URL%
```

## method-signature-no-params-no-body

```ts
%METHOD_NAME%: async (accessToken = '', clientId = ''): ApiResponse<%RESPONSE_TYPE%, %RESPONSE_CODE_SUCCESS%, %RESPONSE_CODE_ERROR%> => 
  this.callApi({
    path: '%PATH%',
    method: '%METHOD%',
    clientId,
    accessToken,
  }),
```

## method-signature-no-params-body

```ts
%METHOD_NAME%: async (
  body: %BODY_TYPE%,
  accessToken = '',
  clientId = '',
): ApiResponse<%RESPONSE_TYPE%, %RESPONSE_CODE_SUCCESS%, %RESPONSE_CODE_ERROR%> => 
  this.callApi({
    path: '%PATH%',
    method: '%METHOD%',
    body,
    clientId,
    accessToken,
  }),
```

## method-signature-params-no-body

```ts
%METHOD_NAME%: async (
  params: %PARAMS_TYPE%,
  accessToken = '',
  clientId = '',
): ApiResponse<%RESPONSE_TYPE%, %RESPONSE_CODE_SUCCESS%, %RESPONSE_CODE_ERROR%> => 
  this.callApi({
    path: '%PATH%',
    method: '%METHOD%',
    params,
    clientId,
    accessToken,
  }),
```

## method-signature-params-body

```ts
%METHOD_NAME%: async (
  params: %PARAMS_TYPE%,
  body: %BODY_TYPE%,
  accessToken = '',
  clientId = '',
): ApiResponse<%RESPONSE_TYPE%, %RESPONSE_CODE_SUCCESS%, %RESPONSE_CODE_ERROR%> => 
  this.callApi({
    path: '%PATH%',
    method: '%METHOD%',
    params,
    body,
    clientId,
    accessToken,
  }),
```
